# Migrating SDL Core 7.0 to 7.1

The 7.1 release had a number of changes and additions to the HMI API that will require updates to your SDL Core integration in your head unit. 

## Environment Update

The default supported version was changed to Ubuntu 20. Recommended GCC Version 9.3.x.

## Newly Deprecated

### Deprecated SyncPData RPCs

- RPC request and response for `EncodedSyncPData` has been marked as deprecated
- RPC notification `OnEncodedSyncPData` has been marked as deprecated

### Deprecated UI params

- `TextFieldName` element `mediaClock` has been marked as deprecated
- `Show` RPC param `mediaClock`has been marked as deprecated
- `RegisterAppInterface` parameters `vehicleType` and `systemSoftwareVersion` has been marked as deprecated. Please make updates to use the parameters from the `StartService ACK` protocol message

### Deprecated Functions

- The function `DynamicApplicationData::IsSubMenuNameAlreadyExist` has been marked as deprecated and should be removed in the next major version change of SDL Core. Please make updates to remove all uses of the function

- The function `ApplicationManagerImpl::OnAppStreaming(uint32_t, protocol_handler::ServiceType, const Application::StreamingState)` has been marked as deprecated and should be removed in the next major version change of SDL Core. Please make updates to use the new function signature `ApplicationManagerImpl::OnAppStreaming(uint32_t, protocol_handler::ServiceType, bool)`

- The function `ProtocolHandlerImpl::NotifySessionStarted(const SessionContext&, std::vector<std::string>&, const std::string)` has been marked as deprecated and should be removed in the next major version change of SDL Core. Please make updates to use the new function signature `ProtocolHandlerImpl::NotifySessionStarted(SessionContext&, std::vector<std::string>&, const std::string)`

- - The function `file_system::ConvertPathForURL` has been marked as deprecated and should be removed in the next major version change of SDL Core. Please make updates to remove all uses of the function

### Deprecated Vehicle Data

- Vehicle Data parameter `externalTemperature` has been deprecated. Please make updates to use the new vehicle data struct `climateData`.

- Vehicle Data parameters `driverDoorAjar`, `passengerDoorAjar`, `rearLeftDoorAjar` and `rearRightDoorAjar` have been deprecated. Please make updates to use the new `doorStatuses` parameter
