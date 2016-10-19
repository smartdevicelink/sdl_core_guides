# SmartDeviceLink 
## Release Notes (Release 4.2.0)

# 1. Introduction

## Definitions and Abbreviations


| *Term*     | *Description*
|:-----------|:--------------|
| CY         | Calendar Year |
| CRQ        | Change request |
| SDL        | SmartDeviceLink |
| ATF        | [Automated Test Framework](https://github.com/smartdevicelink/sdl_atf) |
| GitHub     | [Source code revision system](https://github.com/smartdevicelink/sdl_core/releases/tag/4.2.0) with released version of OpenSDL |


## Scope

### App Launch (iOS): 
 Integration of functionality already implemented in F-S SDL. Within the scope of the CRQ integration, SDL team removeд iAP2 transport implementation and Multiplexing functionality available in F-S SDL. 
 
### New implementation of requested functionality:
* Navigation interface: SDL behavior in case HMI does not respond to IsReady\_request or respond with "available" = false
* TTS interface: SDL behavior in case HMI does not respond to IsReady\_request or respond with "available" = false
* UI interface: SDL behavior in case HMI does not respond to IsReady\_request or respond with "available" = false
* VR interface: SDL behavior in case HMI does not respond to IsReady\_request or respond with "available" = false
* VehicleInfo interface: SDL behavior in case HMI does not respond to IsReady\_request or respond with "available" = false

# 2 About This Release

Implemented functionality for remote launching the applications from the supported Launch function devices.
Note: It is a business logic without supported device implementation. 
For further details, please refer [IAP](https://developer.apple.com/programs/mfi/), [AOA](http://source.android.com/devices/accessories/protocol.html), [SDL Core SAD](https://smartdevicelink.com/en/guides/core/software-architecture-document/components-view/#app-launch) or your transport API documentation.


Changed SDL behavior in case HMI does not respond to IsReady_request or respond with "available" = false for following interfaces: VR, UI, TTS, Navigation and VehicleInfo interfaces.

Short description of new behavior (*INTERFACE* is generic term used to described any of VR, UI, TTS, Navigation and VehicleInfo interfaces):

1. HMI respond *INTERFACE*.IsReady (false) -> SDL must return 'UNSUPPORTED_RESOURCE, success:false' to all single *INTERFACE*-related RPC
2. HMI respond *INTERFACE*.IsReady (false) and app sends RPC that must be splitted -> SDL must NOT transfer *INTERFACE* portion of splitted RPC to HMI
3. HMI does NOT respond to *INTERFACE*.IsReady_request -> SDL must transfer received RPC to HMI even to non-responded *INTERFACE* module
 

# 3 Environment and dependencies

## Development and testing environment for OpenSDL Ubuntu 14.04 LTS x32/x64

- *Debug Environment:* Ubuntu 14.04 LTS x32/x64, Qt 5.3
- *Compiler:* GCC 4.9.3 (OS Ubuntu), Lua 5.2
- *Build system:* Cmake 2.8.12.2

## Development and testing environment for OpenSDL Windows x64:

- *Build system:* Windows 7 x64, CMake
- *Compiler:* Microsoft Visual Studio Express Edition 2013 x64
- *Development and testing environment for OpenSDL Qt for Windows x32:*
- *Build system:* Windows 7 x32, Qt 5.5, CMake, QT Creator
- *Compiler:* Microsoft Visual Studio Express Edition 2010 x32

## *Source Control System:* 
- GitHub

# 4.  Delivery details

## Unit Tests Coverage

| Coverage  | Hit   | Total | Coverage |
|:--------- |:----- |:----- |:-------- |
| Lines     | 18153 | 27828 | 66 % |
| Functions | 7646  | 11830 | 65 % |

## Tests Execution Report

| Tests total | Failures total | Disabled total | Errors total | Total time (milliseconds) | Tests total |
|:----------- |:-------------- |:-------------- |:------------ |:------------------------- |:----------- |
| 1748        | 0              | 16             | 0            | 310482                    | 1748 |

`Brief log of unit test sets execution:`
`01/26 Test #01: test_JSONCPP ......................   Passed    0.18 sec`
`02/26 Test #02: test_generated_interface ..........   Passed    0.09 sec`
`03/26 Test #03: transport_manager_test ............   Passed    1.63 sec`
`04/26 Test #04: resumption_test ...................   Passed    0.06 sec`
`05/26 Test #05: formatters_test ...................   Passed    0.73 sec`
`06/26 Test #06: protocol_handler_test .............   Passed   22.37 sec`
`07/26 Test #07: connection_handler_test ...........   Passed   83.31 sec`
`08/26 Test #08: utils_test ........................   Passed   36.30 sec`
`09/26 Test #09: generator_test ....................   Passed    0.08 sec`
`10/26 Test #10: security_manager_test .............   Passed   10.28 sec`
`11/26 Test #11: policy_test .......................   Passed  108.36 sec`
`12/26 Test #12: rpc_base_test .....................   Passed    0.18 sec`
`13/26 Test #13: smart_object_test .................   Passed    2.23 sec`
`14/26 Test #14: application_manager_test ..........   Passed    3.00 sec`
`15/26 Test #15: resumption/data_resumption_test ...   Passed    0.37 sec`
`16/26 Test #16: state_controller_test .............   Passed    0.37 sec`
`17/26 Test #17: app_launch_ctrl_test ..............   Passed   43.12 sec`
`18/26 Test #18: app_launch_data_test ..............   Passed    0.04 sec`
`19/26 Test #19: commands_test .....................   Passed    0.05 sec`
`20/26 Test #20: mobile_commands_test ..............   Passed    0.19 sec`
`21/26 Test #21: hmi_commands_test .................   Passed    0.05 sec`
`22/26 Test #22: message_helper_test ...............   Passed    0.01 sec`
`23/26 Test #23: hmi_message_handler_test ..........   Passed    0.02 sec`
`24/26 Test #24: config_profile_test ...............   Passed    0.33 sec`
`25/26 Test #25: media_manager_test ................   Passed    0.02 sec`
`26/26 Test #26: telemetry_monitor_test ............   Passed    0.02 sec`
`100% tests passed`

# 5.  Known Bugs and Limitations

All known SDL defects reflected in following chapter. The correction and verification of those defects are out of scope of this release.

## Known Issues

| Summary | Priority |
|:--------|:---------|
| [Genivi]SDL doesn't stop at execution ATF function StopSDL() | Blocker  |
| [Genivi]: Core crash upon Ctrl+C in console | Critical |
| [Genivi][Policies] PTU is not successful due to another unexpected exchange in progress | Critical |
| [Genivi] SDL stops working during processing SetGlobalProperties request | Critical |
| [SDL4.0][Genivi] SDL sends OnSystemRequest(QUERY_APPS) to background on phone App. | Critical |
| [Genivi] [TM] Unable to register iOS App via BT. | Critical |
| [Genivi][Security] SDL do not send certificate from Policy DB and rewrites certificate in module_config with "1" right after using it | Critical |
| [Genivi][Security] SDL crashes if App tries to restore secure RPC service on start | Critical |
| [Genivi][Security] SDL crashes if during TLS handshake ERROR_SSL_INVALID_DATA occurs | Critical |
| [Genivi][Security] SDL crashes if mobile App fails TLS handshake. | Critical |
| [GENIVI][WinQT] 3rd party USB library crash on exit | Critical |
| [Resumption][Genivi] SDL crashes during resumption of 2 Apps, non-media to FULL and media to LIMITED | Critical |
| [GENIVI] SDL should respond "IGNORED" with correct result code for UnSubscribeVehicleData in case vi interface isn't available | Critical |
| [GENIVI][Policy]: SDL does not send RequestType:HTTP in OnSystemRequest to app | Critical |
| APPLINK-17839 Genivi: HMILevel is not resumed to LIMITED for non-media applications | Major |
| APPLINK-17839 Genivi: HMILevel resumption is not canceled at OnEventChanged(AUDIO_SOURCE, isActive: true) | Major |
| [SDL4.0][Genivi] UTF-8: Core incorrect handles symbols of two or more bytes size | Major |
| [Genivi][Policies] SDL does not send OnPermissionsChange after PTU | Major |
| [Genivi][Policies] SDL doesn't exclude messages from snapshot | Major |
| [GENIVI][Policy]: SDL dos not select url from PT for specified appID during GetURLs request. | Major |
| [Genivi] Policies Manager does not revert the Local Policy Table to the Preload Policy Table upon FACTORY Reset | Major |
| [Genivi][Policies] SDL doesn't send "SDL.OnAppPermissionChanged{appID} to HMI | Major |
| [GENIVI][Security]: App continue unprotected stream if start service as protected during active streaming | Major |
| [GENIVI][Memory leaks]: SDL does not release memory after sending AddCommand limit exhausted | Major |
| [Genivi]: SDL send to mobile "APPLICATION_NOT_REGISTRED" in setAppIcon responce if HMI respond with "INVALID_DATA" | Major |
| [GENIVI] Incorrect response on send SystemRequest (file name - /test) | Major |
| [GENIVI] putFile does not copy file with .\\ before the name to AppStorageFolder | Major |
| [GENIVI]SDL retry send StartStream/StartAudioStream less on one time than configured in .ini file | Major |
| [Genivi][Policies] SDL doesn't reject PT if the consumer_friendly_message section contains messaging without “en-us” language key | Major |
| [Genivi][Policies] PM should verify that "seconds_between_retries" array has maxlength 5 | Major |
| [GENIVI] SDL transfer OnKeyboardInput notification to not active App when there is no active PerformInteraction(KEYBOARD) | Major |
| [Genivi] SDL forwards OnButtonPress(CUSTOM_BUTTON) with wrong appID to current App. | Major |
| [Genivi] SDL forwards OnButtonPress notification of CUSTOM_BUTTON to BACKGROUND App. | Major |
| [Genivi] SDL forwards OnButtonEvent notification of CUSTOM_BUTTON to BACKGROUND App. | Major |
| GENIVI: SDL doesn't send "REJECTED" code to mobile app when activating app from HMI with activate Carplay/GAL.GE | Major |
| GENIVI: App is disconnected due to HeartBeat timeout althought HeartBeat is sent. | Major |
| Genivi SDL blocks forever when registering mobile application with Genivi HMI (only) | Major |
| [Genivi] HMI level resumption is not postponded at EmergencyEvent, isActive=true | Major |
| [Genivi] SDL doesn't apply sequence SUSPEND -> OnSDLAwake -> SUSPEND -> IGN_OFF for saving resumption data. | Major |
| [Genivi][API]SDL sends UpdateDeviceList with disconnected device in the deviceList | Major |
| [Genivi]CreateInteractionChoiceSet: core successfully creates choice set with duplicate vrCommands/menuName inside it. | Major |
| [Genivi][SDL4.0]SDL sends appName in vrSynonyms and ttsName in case of lower and upper bound values of params in json file | Major |
| [Genivi][API] App is not unregistered by reason = REQUEST_WHILE_IN_NONE_HMI_LEVEL | Major |
| [Genivi][API] SDL sends OnAppInterfaceUnregistered(DRIVER_DISTRACTION_VIOLATION) to app when receives OnExitApplication(DRIVER_DISTRACTION_VIOLATION) from HMI | Major |
| [GENIVI] One and the Same Correlation_ID is assigned by SDL Two Times | Major |
| [Genivi][SDL4.0]SDL does not send OnSystemRequest to app on second device | Major |
| Genivi: Policy table can't be loaded when RPCs added in functional_group is greater than 50. | Major |
| [GENIVI][Policy]: SDL does not write UserFriendlyMessages to DB | Major |
| GENIVI: PerformAudioThru - SDL does not send "resultCode:RETRY, success:true" to mobile app when press "Retry" button | Major |
| [Genivi][Policy] PM doesn't validate required section/key in case it is invalid and SDL continue running | Major |
| In Genivi (SDL 4) we can have two mobile apps in FULL HMI level at the same time | Major |
| [Genivi][Policies] Ford-specific keys are present in Genivi Policy DB - usage_and_error_counts | Major |
| [Genivi][Policies] PM doesn't update "notifications_per_minute_by_priority" | Major |
| [Genivi][Policies] OnSystemRequest: SDL does not re-send OnSystemRequest notification to mobile app in case when it was sent from HMI without appID | Major |
| Unable to build GENIVI SDL without logs | Major |
| [Genivi] SDL do not apply nicknames after PTU | Major |
| [Genivi] SDL do not disallow API of revoked App. | Major |
| [Genivi] SDL creates redundant device_consent table in Policy DB | Major |
| [Genivi][Protocol] App becomes unregistered if PutFile is sent from any of two sessions (protocols v.2 and v.3) | Major |
| [Genivi][Protocol] SDL respond ACK with protocol version 4 for video and audio services if send start service with 2 or 3 protocol version | Major |
| [Genivi][Policies] SDL should be case-insensetive to "AppID" against listed in policies manager | Major |
| [Genivi][Security] SDL close connection before UNSUPPORTED_VERSION response for RAI was sent. | Major |
| [Genivi][Policies] PT is considered as valid with no items in "groups" sub-section from "default" | Major |
| [Genivi][TV] SDL must respond with INVALID_DATA on SystemRequest that uploads a file containing `../` sequences | Major |
| [Genivi][IVSU] SDL doesn't reject SystemRequest with filenam=IVSU but w/o binary data. | Major |
| [Genivi] Core dump upon FACTORY_DEFAULT | Major |
| [Genivi][Policies] PM doesn't validate the size of section "default" in "endponts" of Policy Table | Major |
| [Genivi][Policy]: PM doesn't merge "functional_grouping" and "message_type" | Major |
| [Genivi][APIs] AlertManeuver: SDL responds GENERIC_ERROR instead of INVALID_DATA when soft button has Type is Image or Both and Text is whitespace or \t or \n or empty | Major |
| [Genivi][API]AlertManeuver:SDL responds to mobile app UNSUPPORTED_REQUEST with success = true | Major |
| [Genivi][SDL4.0] Json validation is failed in case language parameter does not contain vrSynonyms or ttsName | Major |
| [Genivi][SDL4.0]SDL sends OnSystemRequest(QUERY_APPS) to the app after unsuccessful attempt | Major |
| [Genivi]SDL returns IGNORED instead of UNSUPPORTED_RESOURCE for UnsubscribeButton. | Major |
| [Genivi]SDL do not send default vrHelp to HMI if App was registered with vrSynonyms | Major |
| [Genivi] [TM] SDL can't reregister App via USB that was killed before. | Major |
| [Genivi] OnHashChange notification for UnsubscribeVehicleData when 2 applications are registered | Major |
| GENIVI: SDL responds "resultCode: SUCCESS" while dataType:VEHICLEDATA_EXTERNTEMP is VEHICLE_DATA_NOT_AVAILABLE and not in subscribed list store | Major |
| [GENIVI] No response to App on UI.Slider sent if no HMI response during DefaultTimeout | Major |
| [GENIVI][Policy]: SDL does not set "timeout" for OnSystemRequest with url | Major |
| [GENIVI][Policy]: SDL does not apply url from PT for specified appID for OnSystemRequest | Major |
| [Genivi] INVALID_DATA received in case wayPointType with correct parameter was sent | Major |
| [Genivi][API] SDL responds "UNSUPPORTED_RESOURCE", success= false in case only have "UNSUPPORTED_RESOURCE" to Navigation.AlertManeuver | Major |
| GENIVI: App is disconnected due to PROTOCOL_VIOLATION when start audio streaming after rejected 2 times then accepted. | Major |
| [GENIVI]: SDL crashes during execution ATF test with start-stop SDL cases | Major |
| [GENIVI] Redundant info is sent to App when single UI. got 'UNSUPPORTED_RESOURCE' from HMI | Major |
| [Genivi] SDL responses with GENERIC_ERROR instead of UNSUPPORTED_RESOURCE | Major |
| [GENIVI] SDL does not transfer all info parameters of unsuccess result codes when UI.IsReady = false | Major |
| [GENIVI] SDL does not respond info message in case GENERIC_ERROR watchdog timeout from HM | Major |
| GENIVI: SDL does not send VehicleInfo.GetVehicleData in case HMI responds invalid json for TTS.IsReady and true for VehicleInfo.IsReady | Major |
| [GENIVI][Navigation] SDL does not respond info message in case GENERIC_ERROR watchdog timeout from HM | Major |
| [GENIVI] GetWayPoints: SDL does not reset the default watchdog timeout of GetWayPoints request if HMI sends OnResetTimeout notification for this request | Major |
| [GENIVI] HashID should not be updated on successful single UI. if no UI.IsReady response | Major |
| [GENIVI] USER_DISALLOWED send instead of IGNORED for UnsubscribedVehicleData when VehicleInfo IsReady Missing | Major |
| [GENIVI] SDL responds "WARNINGS" code in case SDL got "WARNINGS" from TTS and error code from other interfaces. | Major |
| [GENIVI] SDL doesn't set unsuccessful "message" value to "info" param in case HMI responds via single UI.RPC when .IsReady missing | Major |
| [Policies][Genivi] PM shuts SDL down but doesn't log error in case any required section/key is absent in the sdl_prealoaded_pt.json | Normal |
| [Genivi] OpenSDL repo still contains old "generate_test_certificates.py" script. | Normal |
| GENIVI: SDL does not send StopAudioStream() if exit app while Video service and Audio service are starting. | Normal |
| [GENIVI]The session registration is delayed by locks | Normal |
| [Genivi][SDL4.0]SDL does not create icons folder in case it was removed after SDL start | Normal |
| [Genivi][API] SDL sends BC.UpdateDeviceList with out of upper bound size of deviceList | Normal |
| [Genivi][SDL4.0] SDL sends wrong parameter names in OnSystemRequest(query_apps) | Normal |
| [GENIVI][Policy]: SDL increments "ignition_cycles_since_last_exchange" counter after ign cycle if PTU was not before | Normal |
| [SDL4.0][Genivi] incorrect resp. in case Unsubscribe not supported and not yet subscribed button | Normal |
| [Genivi][Policies] Ford-specific keys are present in Genivi Policy DB - DeviceData | Normal |
| [Genivi][Policies]: "application" table contains "certificate" | Normal |
| [Genivi] SDL doesn't add device identifier to Policy DB and not present in the snapshot | Normal |
| [Genivi][Security] SDL print out CN and serialNumber details of certificates in log. | Normal |
| [Genivi][VS][AS] SDL does not send StopStream/StopAudioStream to HMI after unregistering app during streaming | Normal |
| [Genivi] Communication is not saved in SmartDeviceLinkCore.log after adding persistent data after SUSPEND | Normal |
| [Genivi]SDL doesn't transfer info parameter with UI.SetGlobalProperties to mobile. | Normal |
| [Genivi]SDL sends systemSoftwareVersion (with empty value) in RAI response if before ccpu_version was invalid in GetSystemInfo. | Normal |
| [Genivi][Policies] Policy table is not initialized by SDL start without DB | Normal |
| [GENIVI] Response to UI.ScrollableMessage is sent twice to app if no HMI response HMI during | Normal |
| [Genivi]SDL doesn't send info parameter when result of ResetGlobalProperties is GENERIC_ERROR | Normal |
| Genivi: UI is waiting for TTS.IsReady timeout to elapse to send UI. | Normal |
| [Genivi] SDL response success:false to mobile app in case it received RETRY or WRONG_LANGUAGE or UNSUPPORTED_RESOURCE from HMI | Normal |
| GENIVI: SDL always reponds "INVALID_DATA" to mobile app while receiving other code from HMI | Normal |
| [Genivi] Incorrect Version of API in MOBILE_API.xml | Normal |
| [GENIVI] SDL build without any error message with empty version in MOBILE_API.xml | Minor |
| [GENIVI] SyncMsgVersion is not updated after RegisterAppInterface | Minor |


