# Remote Control Guide

Hello, this guide will explain how Remote Control works within SDL. The guide will cover...

- Relevant proposals
- Relevant structs
- Relevant RPCs
- Modules and their components
- Consent matrix
- Limiting permissions with policies

#### Relevant Evolution Proposals

- [0071: Remote Control Baseline](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0071-remote-control-baseline.md)
- [0099: New Remote Control Modules and Parameters](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0099-new-remote-control-modules-and-parameters.md)
- [0105: Remote Control - Seat](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0105-remote-control-seat.md)
- [0106: Remote Control - OnRCStatus notification](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0106-remote-control-onRcStatus-notification.md)
- [0160: Remote Control - Radio Parameter Update](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0160-rc-radio-parameter-update.md)
- [0165: Remote Control - More Light Names and Status Values](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0165-rc-lights-more-names-and-status-values.md)
- [0172: Remote Control - OnRCStatus allowed](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0172-onRcStatus-allowed.md)
- [0181: Remote Control - When RC Disabled, Apps Keep HMI Level](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0181-keep-rc-app-hmi-level-when-disable-rc.md)
- [0213: Remote Control - Radio and Climate Parameter Update](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0213-rc-radio-climate-parameter-update.md)
- [0221: Remote Control - Allow Multiple Modules per Module Type](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0221-multiple-modules.md)

## Relevant Structs

[RemoteControlCapabilities](../../hmi/common/structs/#remotecontrolcapabilities)
The remote control capabilities struct contains a capabilities struct for each different remote control type.

Each capabilities struct is used to inform an app of what is available to be controlled.
- [ClimateControlCapabilities](../../hmi/common/structs/#climatecontrolcapabilities)
- [RadioControlCapabilities](../../hmi/common/structs/#radiocontrolcapabilities)
- [SeatControlCapabilities](../../hmi/common/structs/#seatcontrolcapabilities)
- [AudioControlCapabilities](../../hmi/common/structs/#audiocontrolcapabilities)
- [HMISettingsControlCapabilities](../../hmi/common/structs/#hmisettingscontrolcapabilities)
- [LightControlCapabilities](../../hmi/common/structs/#lightcontrolcapabilities)
- [ButtonCapabilities](../../hmi/common/structs/#buttoncapabilities)

[ModuleData](../../hmi/common/structs/#moduledata)
The module data struct contains information used to identify a module type, and the control data associated with that module.

Each control data struct is used to observe or change the attributes of a specific module type.
- [RadioControlData](../../hmi/common/structs/#radiocontroldata)
- [ClimateControlData](../../hmi/common/structs/#climatecontroldata)
- [SeatControlData](../../hmi/common/structs/#seatcontroldata)
- [AudioControlData](../../hmi/common/structs/#audiocontroldata)
- [LightControlData](../../hmi/common/structs/#lightcontroldata)
- [HMISettingsControlData](../../hmi/common/structs/#hmisettingscontroldata)

[ModuleInfo](../../hmi/common/structs/#moduleinfo)
The module information struct is used for identifying the module and for determining who can control it.

[Grid](../../hmi/common/structs/#grid)
The grid struct is used to generically describe the space within a vehicle.

## Relevant RPCs

### IsReady

After the `BC.IsReady` notification is received, SDL will send out an `IsReady` request for each interface. The response to this RPC just includes the boolean parameter `available` indicating if the HMI supports that interface and would like to continue to interact with it.

[View `IsReady` in the HMI Documentation](../../hmi/rc/isready)

### GetCapabilities

Once SDL has received a positive `IsReady` response it will send a `GetCapabilities` request to the HMI. The HMI should respond with a `RemoteControlCapabilities` parameter for SDL to store and use later when a mobile application sends a `GetSystemCapability` request. This will overwrite the capabilities SDL loaded from the `hmi_capabilities.json` configuration file.

[View `GetCapabilities` in the HMI Documentation](../../hmi/rc/getcapabilities)

### GetSystemCapability

This RPC is the starting point for an app using remote control features, it will tell you what is available to be controlled within the vehicle. GetSystemCapability is not specific to Remote Control, but a generic function used to retreive the capabilities of multiple different modules within SDL such as navigation, video streaming or app services. However, when GetSystemCapability is called with the capability type of `REMOTE_CONTROL`, it will return the `RemoteControlCapabilities` object which in turn contains objects describing the capabilities of each remote control module present in the vehicle. These capabilities objects will contain properties like `heatedMirrorsAvailable` to indicate if a vehicle is equipped with heated mirrors, or `supportedLights` to inform SDL of which lights are available to be controlled.

[View `GetSystemCapability` in the HMI Documentation](../../hmi/rc/getsystemcapability)

### GetInteriorVehicleData

GetInteriorVehicleData is used to request information about a specific module. This RPC, provided a module is specified by `moduleType` and `moduleId`, will return the status of the module and if relevant, its' submodules. This RPC can also be used to subscribe to updates of a module's status via the `subscribe` parameter. If this non-mandatory parameter is set to true, the head unit will register `OnInteriorVehicleData` notifications for the requested module. Conversely, if this parameter is set to false, the head unit will unregister `OnInteriorVehicleData` notifications for the requested module.

[View `GetInteriorVehicleData` in the HMI Documentation](../../hmi/rc/getinteriorvehicledata)

### OnInteriorVehicleData

OnInteriorVehicleData is a notification sent out by SDL when an update is made to a remote control module. You can subscribe to these notifications via GetInteriorVehicleData. This RPC will come with a `ModuleData` structure identifying the changed module and containing the control data object with the new state.

[View `OnInteriorVehicleData` in the HMI Documentation](../../hmi/rc/oninteriorvehicledata)

### SetInteriorVehicleData

SetInteriorVehicleData is used to set the values of a remote control module by passing in a `ModuleData` structure. The `moduleType` and `moduleId` fields are used to identify the targeted module, and the changes in the respective control data object are applied to that module.

[View `SetInteriorVehicleData` in the HMI Documentation](../../hmi/rc/setinteriorvehicledata)

### OnRemoteControlSettings

OnRemoteControlSettings is used to notify SDL when passengers of a vehicle change whether remote control is allowed or not via the HMI or if they change the access mode that will be used for resource allocation. It contains a self-explainatory boolean parameter `allowed` and can also contain an access mode, one of auto allow, auto deny, or ask driver.

[View `OnRemoteControlSettings` in the HMI Documentation](../../hmi/rc/onremotecontrolsettings)

### OnRCStatus

OnRCStatus is a notification sent out by SDL when an update is made to a remote control module's availability. When SDL either allocates a module to an app, or deallocates it from an app, SDL will send OnRCStatus to both the application and the HMI. This notification contains two lists, one describing the modules that are allocated to the application and the other describing the free modules that can be accessed by the application. This notification also contains an `allowed` parameter, which indicates to apps whether or not remote control is currently allowed. If `allowed` is false, both module lists will be empty.
[View `OnRCStatus` in the HMI Documentation](../../hmi/rc/onrcstatus]

### GetInteriorVehicleDataConsent

GetInteriorVehicleDataConsent is a request used to reserve a remote control module. If a module does not allow multiple access, only the application that requested consent first (excluding takeover situations described in the Consent section) will be able to interact with that module. This request requires a `moduleType` and `moduleId` to identify the target module.

[View `GetInteriorVehicleDataConsent` in the HMI Documentation](../../hmi/rc/getinteriorvehicledataconsent)

### ReleaseInteriorVehicleDataModule

ReleaseInteriorVehicleDataModule is a request used to free a remote control module once an application is finished interacting with it. This request requires a `moduleType` and `moduleId` to identify the target module.

[View `ReleaseInteriorVehicleDataModule` in the HMI Documentation](../../hmi/rc/releaseinteriorvehicledatamodule)

### SetGlobalProperties

SetGlobalProperties is a request sent by a mobile app to inform SDL of a user's location within the vehicle. The request includes an `appId` and a `userLocation` parameter which contains a grid. The location of a user is important for SDL to know so it can determine whether or not a user is within a module's service area.

[View `SetGlobalProperties` in the HMI Documentation](../../hmi/rc/setglobalproperties)

## Remote Control Modules

### Climate

The climate module consists of climate sub-modules represented by a `ClimateControlCapabilities` object. Each sub-module exposes many aspects of a car's climate controls, such as setting the desired temperature or turning on the heated windshield. 

### Radio

The radio module consists of radio sub-modules represented by a `RadioControlCapabilities` object. Each sub-module exposes many aspects of a car's radio controls, such as setting the desired frequency and band the radio is operating on.

### Seat

The seat module consists of seat sub-modules repesented by a `SeatControlCapabilities` object. Each sub-module exposes many aspects of a car's seat controls, such as setting the back tilt angle and the massage mode.

### Audio

The audio module consists of audio sub-modules represented by a `AudioControlCapabilities` object. Each sub-module exposes many aspects of a car's audio controls, such as setting the volume or modifying the equalizer settings.

### Light

The light module does not contain any sub-modules but instead has an array of `LightCapabilities` objects, each identified by a `LightName`. This module exposes the ability to modify attributes such as the brightness and color of each light.

### HMI Settings

The HMI settings module does not contain any sub-modules and is represented by an `HMISettingsControlCapabilities` object. This module exposes the the ability to set the desired temperature and distance units as well as toggle the display mode of the HMI between night and day.

### Button

Button is an interesting remote control component because it is not a remote control module. `RemoteControlCapabilities` includes an array of `ButtonCapabilities` structs which describe either a physical button or a softbutton. A mobile app may send a `ButtonPress` RPC with the `ButtonName` and `moduleId` from any of these `ButtonCapabilities` to perform an action on another remote control module.

## Consent

The behavior of module allocation in SDL core is shown in the following table:

!!! NOTE
The driver is always considered to be within the service area.
SDL will assume actions performed by the driver are consented to by the driver.
!!!

|User Location|Allow Multiple Access|Requesting App HMI Level|Requested Module State|Access Mode|SDL Action|
|:-----|:---|:---|:---|:---|:---|
|out of service area|any|any|any|any|disallow|
|in service area|true|any|any|any|allow|
|in service area|false|any|free|any|allow|
|in service area|false|any|busy|any|disallow|
|in service area|false|any|in use|any|disallow|
|in service area|true|BACKGROUND or LIMITED|in use|any|disallow|
|in service area|true|FULL|in use|auto allow|allow|
|in service area|true|FULL|in use|auto deny|disallow|
|in service area|true|FULL|in use|ask driver|ask driver|

#### Requested Module State

"free" indicated no application currently holds the requested resource
"in use" means that an application currently holds the requested resource
"busy" means at least one RC RPC request is currently executing and has yet to finish

## Policies

You can take a look at the [Remote Control section of the policies guide](../../sdl-overview-guides/policies/app-policies/#remote-control-fields) to see remote control permissions are defined.
