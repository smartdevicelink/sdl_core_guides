# Remote Control Guide

This guide will explain how to use Remote Control within SDL. The guide will cover...

- [Relevant proposals](#relevant-evolution-proposals)
- [Relevant structs](#relevant-structs)
- [Relevant RPCs](#relevant-rpcs)
- [Modules and their components](#remote-control-modules)
- [Consent rules](#consent)
- [Limiting permissions with policies](#policies)

#### Relevant Evolution Proposals

- [0071: Remote Control Baseline](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0071-remote-control-baseline.md)
- [0099: New Remote Control Modules and Parameters](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0099-new-remote-control-modules-and-parameters.md)
- [0105: Remote Control - Seat](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0105-remote-control-seat.md)
- [0106: Remote Control - OnRCStatus notification](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0106-remote-control-onRcStatus-notification.md)
- [0160: Remote Control - Radio Parameter Update](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0160-rc-radio-parameter-update.md)
- [0165: Remote Control - More Light Names and Status Values](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0165-rc-lights-more-names-and-status-values.md)
- [0172: Remote Control - OnRCStatus Allowed Parameter](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0172-onRcStatus-allowed.md)
- [0181: Remote Control - When RC Disabled, Apps Keep HMI Level](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0181-keep-rc-app-hmi-level-when-disable-rc.md)
- [0213: Remote Control - Radio and Climate Parameter Update](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0213-rc-radio-climate-parameter-update.md)
- [0221: Remote Control - Allow Multiple Modules per Module Type](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0221-multiple-modules.md)

## Relevant Structs

### [**RemoteControlCapabilities**](https://smartdevicelink.com/en/guides/hmi/common/structs/#remotecontrolcapabilities)
The remote control capabilities struct contains a capabilities struct for each different remote control type.

Each capabilities struct is used to inform an app of what is available to be controlled.

* [RadioControlCapabilities](https://smartdevicelink.com/en/guides/hmi/common/structs/#radiocontrolcapabilities)
* [ClimateControlCapabilities](https://smartdevicelink.com/en/guides/hmi/common/structs/#climatecontrolcapabilities)
* [SeatControlCapabilities](https://smartdevicelink.com/en/guides/hmi/common/structs/#seatcontrolcapabilities)
* [AudioControlCapabilities](https://smartdevicelink.com/en/guides/hmi/common/structs/#audiocontrolcapabilities)
* [LightControlCapabilities](https://smartdevicelink.com/en/guides/hmi/common/structs/#lightcontrolcapabilities)
* [HMISettingsControlCapabilities](https://smartdevicelink.com/en/guides/hmi/common/structs/#hmisettingscontrolcapabilities)
* [ButtonCapabilities](https://smartdevicelink.com/en/guides/hmi/common/structs/#buttoncapabilities)

### [**ModuleData**](https://smartdevicelink.com/en/guides/hmi/common/structs/#moduledata)
The module data struct contains information used to identify a module type, and the control data associated with that module.

Each control data struct is used to observe or change the attributes of a specific module type.

* [RadioControlData](https://smartdevicelink.com/en/guides/hmi/common/structs/#radiocontroldata)
* [ClimateControlData](https://smartdevicelink.com/en/guides/hmi/common/structs/#climatecontroldata)
* [SeatControlData](https://smartdevicelink.com/en/guides/hmi/common/structs/#seatcontroldata)
* [AudioControlData](https://smartdevicelink.com/en/guides/hmi/common/structs/#audiocontroldata)
* [LightControlData](https://smartdevicelink.com/en/guides/hmi/common/structs/#lightcontroldata)
* [HMISettingsControlData](https://smartdevicelink.com/en/guides/hmi/common/structs/#hmisettingscontroldata)

[**ModuleInfo**](https://smartdevicelink.com/en/guides/hmi/common/structs/#moduleinfo)
The module information struct is used for identifying the module and for determining who can control it.

[**Grid**](https://smartdevicelink.com/en/guides/hmi/common/structs/#grid)
The grid struct is used to generically describe the space within a vehicle.

## Relevant RPCs

### IsReady

After the `BC.IsReady` notification is received, SDL will send out an `IsReady` request for each interface. The response to this RPC just includes the boolean parameter `available` indicating if the HMI supports that interface and would like to continue to interact with it.

View **IsReady** in the [HMI Documentation](https://smartdevicelink.com/en/guides/hmi/rc/isready)

### GetCapabilities

Once SDL has received a positive `IsReady` response it will send a `GetCapabilities` request to the HMI. The HMI should respond with a `RemoteControlCapabilities` parameter for SDL to store and use later when a mobile application sends a `GetSystemCapability` request. This will overwrite the capabilities SDL loaded from the `hmi_capabilities.json` configuration file.

View **GetCapabilities** in the [HMI Documentation](https://smartdevicelink.com/en/guides/hmi/rc/getcapabilities)

### GetSystemCapability

This RPC is the starting point for an app using remote control features, it will tell you what is available to be controlled within the vehicle. GetSystemCapability is not specific to Remote Control, but a generic function used to retrieve the capabilities of multiple different modules within SDL such as navigation, video streaming or app services. However, when GetSystemCapability is called with the capability type of `REMOTE_CONTROL`, it will return the `RemoteControlCapabilities` object which in turn contains objects describing the capabilities of each remote control module present in the vehicle. These capabilities objects will contain properties like `heatedMirrorsAvailable` to indicate if a vehicle is equipped with heated mirrors, or `supportedLights` to inform SDL of which lights are available to be controlled.

View **GetSystemCapability** in the [RPC Spec](https://github.com/smartdevicelink/rpc_spec#getsystemcapability)

### GetInteriorVehicleData

GetInteriorVehicleData is used to request information about a specific module. This RPC, provided a module is specified by `moduleType` and `moduleId`, will return the status of the requested remote-control module. This RPC can also be used to subscribe to updates of a module's status via the `subscribe` parameter. If this non-mandatory parameter is set to true, the head unit will register `OnInteriorVehicleData` notifications for the requested module. Conversely, if this parameter is set to false, the head unit will unregister `OnInteriorVehicleData` notifications for the requested module.

View **GetInteriorVehicleData** in the [RPC Spec](https://github.com/smartdevicelink/rpc_spec#getinteriorvehicledata) or the [HMI Documentation](https://smartdevicelink.com/en/guides/hmi/rc/getinteriorvehicledata)

### OnInteriorVehicleData

OnInteriorVehicleData is a notification sent out by the HMI when an update is made to a remote control module. An app can subscribe to these notifications via GetInteriorVehicleData. This RPC will come with a `ModuleData` structure identifying the changed module and containing the control data object with the new state.

View **OnInteriorVehicleData** in the [RPC Spec](https://github.com/smartdevicelink/rpc_spec#oninteriorvehicledata) or the [HMI Documentation](https://smartdevicelink.com/en/guides/hmi/rc/oninteriorvehicledata)

### SetInteriorVehicleData

SetInteriorVehicleData is used to set the values of a remote control module by passing in a `ModuleData` structure. The `moduleType` and `moduleId` fields are used to identify the targeted module, and the changes in the respective control data object are applied to that module.

View **SetInteriorVehicleData** in the [RPC Spec](https://github.com/smartdevicelink/rpc_spec#setinteriorvehicledata) or the [HMI Documentation](https://smartdevicelink.com/en/guides/hmi/rc/setinteriorvehicledata)

### OnRemoteControlSettings

OnRemoteControlSettings is used to notify SDL when passengers of a vehicle change the remote control settings via the HMI. This includes allowing or disallowing Remote Control or changing the access mode that will be used for resource allocation.

View **OnRemoteControlSettings** in the [HMI Documentation](https://smartdevicelink.com/en/guides/hmi/rc/onremotecontrolsettings)

### OnRCStatus

OnRCStatus is a notification sent out by SDL when an update is made to a remote control module's availability. When SDL either allocates a module to an app, or deallocates it from an app, SDL will send OnRCStatus to both the application and the HMI. This notification contains two lists, one describing the modules that are allocated to the application and the other describing the free modules that can be accessed by the application. This notification also contains an `allowed` parameter, which indicates to apps whether or not Remote Control is currently allowed. If `allowed` is false, both module lists will be empty.

View **OnRCStatus** in the [RPC Spec](https://github.com/smartdevicelink/rpc_spec#onrcstatus) or the [HMI Documentation](https://smartdevicelink.com/en/guides/hmi/rc/onrcstatus)

### GetInteriorVehicleDataConsent

GetInteriorVehicleDataConsent is a request used to reserve remote control modules. If a module does not allow multiple access, only the application that requested consent first will be able to interact with that module. Otherwise, if the module does allow multiple access, the rules specified in the [Consent section](#consent)) apply. This request requires a `moduleType` and an array of `moduleId`s to identify the target modules. Core will reply with an array of booleans indicating the consent for each requested `moduleId` where true signals allowed and vice versa.

View **GetInteriorVehicleDataConsent** in the [RPC Spec](https://github.com/smartdevicelink/rpc_spec#getinteriorvehicledataconsent) or the [HMI Documentation](https://smartdevicelink.com/en/guides/hmi/rc/getinteriorvehicledataconsent)

### ReleaseInteriorVehicleDataModule

ReleaseInteriorVehicleDataModule is a request used to free a remote control module once an application is finished interacting with it. This request requires a `moduleType` and `moduleId` to identify the target module.

View **ReleaseInteriorVehicleDataModule** in the [RPC Spec](https://github.com/smartdevicelink/rpc_spec#releaseinteriorvehicledatamodule)

### SetGlobalProperties

SetGlobalProperties is a request sent by a mobile app to inform SDL of a user's location within the vehicle. The request includes a `userLocation` parameter which contains a grid. The location of a user is important for SDL to know so it can determine whether or not a user is within a module's service area.

View **SetGlobalProperties** in the [RPC Spec](https://github.com/smartdevicelink/rpc_spec#setglobalproperties) or the [HMI Documentation](https://smartdevicelink.com/en/guides/hmi/rc/setglobalproperties)

## Remote Control Modules

### Climate

The climate module consists of climate sub-modules represented by a `ClimateControlCapabilities` object. Each sub-module exposes many aspects of a car's climate controls, such as setting the desired temperature or turning on the heated windshield. 

### Radio

The radio module consists of radio sub-modules represented by a `RadioControlCapabilities` object. Each sub-module exposes many aspects of a car's radio controls, such as setting the desired frequency and band the radio is operating on.

### Seat

The seat module consists of seat sub-modules represented by a `SeatControlCapabilities` object. Each sub-module exposes many aspects of a car's seat controls, such as setting the back tilt angle and the massage mode.

### Audio

The audio module consists of audio sub-modules represented by a `AudioControlCapabilities` object. Each sub-module exposes many aspects of a car's audio controls, such as setting the volume or modifying the equalizer settings.

### Light

The light module does not contain any sub-modules but instead has an array of `LightCapabilities` objects, each identified by a `LightName`. This module exposes the ability to modify attributes such as the brightness and color of each light.

### HMI Settings

The HMI settings module does not contain any sub-modules and is represented by an `HMISettingsControlCapabilities` object. This module exposes the the ability to set the desired temperature and distance units as well as toggle the display mode of the HMI between night and day.

### Button

Button is an interesting remote control component because it is not a remote control module. `RemoteControlCapabilities` includes an array of `ButtonCapabilities` structs which describe either a physical button or a softbutton. A mobile app may send a `ButtonPress` RPC with the `ButtonName` and `moduleId` from any of these `ButtonCapabilities` to perform an action on another remote control module.

## Consent

The behavior of module allocation in SDL Core is shown in the following table:

!!! NOTE
The driver is always considered to be within the service area.
SDL will assume actions performed by the driver are consented to by the driver.
Resources can only be acquired by apps in HMI level full.
!!!

|User Location|Allow Multiple Access|Requested Module State|Access Mode|SDL Action|
|:-----|:---|:---|:---|:---|
|out of service area|any|any|any|disallow|
|in service area|true|any|any|allow|
|in service area|false|free|any|allow|
|in service area|false|busy|any|disallow|
|in service area|false|in use|any|disallow|
|in service area|true|in use|auto allow|allow|
|in service area|true|in use|auto deny|disallow|
|in service area|true|in use|ask driver|ask driver|

#### Requested Module State

* "free" indicates no application currently holds the requested resource
* "in use" indicates that an application currently holds the requested resource
* "busy" indicates at least one RC RPC request is currently executing and has yet to finish

## Policies

You can take a look at the [Remote Control section of the policies guide](https://smartdevicelink.com/en/guides/sdl-overview-guides/policies/app-policies/#remote-control-fields) to see how remote control permissions are defined.
