# Resume Controller

This page will descrive internal structure and detailed design of Resume controller 

|||
Figure 1: ResumeController Overview
![ResumeControllerClasses](./assets/resume_controller_classes.png)
|||

!!! NOTE
Classes named like *Impl only _represent_ implementations of the abstract sub classes and may not be named the same in the SDL Core project.
UML Refresher
* Aggregation: Solid line with open diamond
* Composition: Solid line with filled diamond
* Inheritance: Dotted line with open arrow
* Dependency: Dotted line with two prong arrow
!!!

## Resume Controller

SDL supports 2 resumption types : 
 * HMI Level resumption
 * Data resumption

### Data resumption

SDL restore application data if application send appropriate `hash` in RAI. Hash updates after each data change. 
SDL stores resumption data either in json or in database, this option is configurable. 

ResumeControllerImpl request app data from `ResumptionData` class and provide it to `ResumptionDataProcessor`

`ResumptionDataProcessor` is responcible for restoring application data and provide result to RAI via callback

|||
Figure 2: Resumption data seauence Overview
![Resumption data seauence](./assets/data_resumption.png)
|||


### ResumptionData

`ResumptionData` class user to represend resumption data agnostic to data storage. 
`ResumptionData` provides app resumption data as smart objects. 

|||
Figure 2: Resumption data classes
![Resumption data classes](./assets/resumption_data_class.png)
|||

There are 2 implementations of resumption data : 
 * `ResumptionDataJson`
 * `ResumptionDataDB`

 `ResumptionData` does not contains and active components : timers, reactions, callbacks, etc ...
 It behaves like data a storage.

### ResumptionDataProcessor

ResumptionDataProcessor responcible for restoring resumption data and track it's status. 

Main public function for resumptions is `ResumptionDataProcessor::Restore` : 
```cpp
  /**
  * @brief Running resumption data process from saved_app to application.
  * @param application application which will be resumed
  * @param saved_app application specific section from backup file
  */
  void Restore(app_mngr::ApplicationSharedPtr application,
               smart_objects::SmartObject& saved_app,
               ResumeCtrl::ResumptionCallBack callback);
```

`ResumeCtrl::ResumptionCallBack callback` is a function that should be called after data resumption : 
```cpp
typedef std::function<void(mobile_apis::Result::eType result_code,
                             const std::string& info)> ResumptionCallBack;
```

Some resumption data should be restored in `Application` class it self.
Some resumption data should stored in pligins : ApplicationExtensions. 
Some resumption data requires sending HMI request. 

ResumptionDataProcessor is inherited from EventObserver to track responses. 

If responses are succesfull ResumptionDataProcessor just call `callback(SUCCESS)`

If part of the data was failed to restore, ResumptionDataProcessor should revert already restored data and call  `callback(ERROR_CODE, info)`.

Requirenments available in proposal [Handle response from HMI during resumption data](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0190-resumption-data-error-handling.md)

RAI will wait for callback to send response to mobile App.

### AppExtension

Application extension contains follwowing methods for resumption : 
```cpp
  /**
   * @brief SaveResumptionData method called by SDL when it saves resumption
   * data.
   * @param resumption_data data reference to data, that will be appended by
   * plugin
   */
  virtual void SaveResumptionData(
      smart_objects::SmartObject& resumption_data) = 0;

  /**
   * @brief ProcessResumption Method called by SDL during resumption.
   * @param resumption_data list of resumption data
   * @param subscriber callbacks for subscribing
   */
  virtual void ProcessResumption(
      const smart_objects::SmartObject& resumption_data,
      resumption::Subscriber subscriber) = 0;

  /**
   * @brief RevertResumption Method called by SDL during revert resumption.
   * @param subscriptions Subscriptions from which must discard
   */
  virtual void RevertResumption(
      const smart_objects::SmartObject& subscriptions) = 0;
```

Only app extension have an access to active data, data send and data revert process. 
Each app extension use own plugin to manipulate with functionality. 

`SaveResumptionData` will fill passwd resumption_data for saving to `ResumptionData`. 

|||
Example from VehicleInfoAppExtension: 
```cpp
SDLRPCPlugin& plugin_;
...
void VehicleInfoAppExtension::SaveResumptionData(
    smart_objects::SmartObject& resumption_data) {
  resumption_data[strings::application_vehicle_info] =
      smart_objects::SmartObject(smart_objects::SmartType_Array);
  int i = 0;
  for (const auto& subscription : subscribed_data_) {
    resumption_data[strings::application_vehicle_info][i++] = subscription;
  }
}
```
|||

`ProcessResumption` will send appropriate hmi requests, and change internal SDL state according to provided `resumption_data`. All HMI responses will be transfered to `subscriber`. 

|||
Example from SDLAppExtension: 
```cpp
SDLRPCPlugin& plugin_;
...
void SDLAppExtension::ProcessResumption(
    const smart_objects::SmartObject& saved_app,
    resumption::Subscriber subscriber) {
  ...
  const bool subscribed_for_way_points_so =
      saved_app[strings::subscribed_for_way_points].asBool();
  if (subscribed_for_way_points_so) {
    plugin_.ProcessResumptionSubscription(app_, *this, subscriber);
  }
}
```
|||

`RevertResumption` will send appropriate hmi requests to revert provided `subscriptions`.


`ResumptionDataProcessor` go throw all `application->Extensions` to track responses and setup itself as `subscriber` : 

```cpp
void ResumptionDataProcessor::AddPluginsSubscriptions(
    ApplicationSharedPtr application,
    const smart_objects::SmartObject& saved_app) {
  LOG4CXX_AUTO_TRACE(logger_);

  for (auto& extension : application->Extensions()) {
    extension->ProcessResumption(
        saved_app,
        [this](const int32_t app_id, const ResumptionRequest request) {
          this->WaitForResponse(app_id, request);
        });
  }
}
```
