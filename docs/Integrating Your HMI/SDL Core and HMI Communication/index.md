# SDL Core and HMI Communication

## Connecting HMI to SDL

WebSocket is the primary means of communicating with the SDL Core component from the vehicle. In a basic example, an HTML5 HMI would use a native WebSocket library to communicate with SDL Core.

The HMI Adapter must:

!!! must
  * Be installed on the same vehicle HU OS where SDL Core is installed, or the HMI must be able to be networked to SDL Core and address it via a static IP address.
  * Create and initialize components which are defined in the HMI_API specification for the version of SDL Core which is running on the vehicle HU. (For example: BasicCommunication, UI, Buttons, VR, TTS, Navigation, VehicleInfo, RC, AppService)
  * Establish a separate WebSocket connection with SDL Core for each of components defined in the HMI_API specification.
  * Use the appropriate corresponding connection when sending responses and notifications to any connected component.

!!!

## Handshake
For opening a WebSocket connection, a handshake must be performed.

!!! info
  1. Client/Server relationship
    * SDL Core is the Server
    * The HMI is the Client
  2. Host
    * SDL Core is listening on **127.0.0.1:8087** by default
    * The IP and port are configurable in SDL Core's smartDeviceLink.ini file
  3. WebSocket Protocol Version 13 is used by SDL Core

!!!

### Example: Connecting to SDL Core with Javascript

```js
connectToSDL() {
  this.socket = new WebSocket("ws://localhost:8087")
  this.socket.onopen = this.onopen.bind(this)
  this.socket.onclose = this.onclose.bind(this)
  this.socket.onmessage = this.onmessage.bind(this)
}
```

!!! note
SDL Core accepts multiple WebSocket clients and the HMI can choose to connect each interface to SDL Core via individual WebSocket connections.
!!!

## Component Registration

### Request

The HMI must register each component which can communicate with SDL Core using the following RPC format.

| Key     | Value Info    |
| :------------- | :------------- |
| id       | A multiple of 100 (100, 200, 300, ...) |
| jsonrpc | "2.0" - constant for all messages between SDL Core and the HMI|
| method | "MB.registerComponent" - the request is assigned to SDL Core's MessageBroker where the component name will be associated with the socket ID. Further, SDL Core will send messages related to the named component over the corresponding connection|
| componentName| The name of the component being registered. Must correspond to the appropriate component name described in the current guidelines.|

Example Request:
```json
{
  "jsonrpc": "2.0",
  "id": 100,
  "method": "MB.registerComponent",
  "params": {
    "componentName": "BasicCommunication"
  }
}
```

The possible componentNames are:  

  * `BasicCommunication` - Generic interface containing RPCs related to HMI management. Functionality includes managing the app and device lists, opening and closing apps, SDL life cycle updates, getting system info, and system requests. This interface also contains some other one off RPCs like `DialNumber` and `GetSystemTime`.
  * `UI` - Interface responsible for RPC events and information made visible to the user. Functionality includes getting the display capabilities, changing the app template, managing the in app menus, popups, touch events, and changing the language. It also includes the `PerformAudioPassThru` RPC used to capture user's speech.
  * `Buttons` - Interface responsible for RPC events and information related to hard and soft buttons in the vehicle. Includes `OnButtonPress` and `OnButtonEvent`.
  * `VR` - Interface responsible for RPC events and information related to voice recognition. Functionality includes managing voice commands, creating a `PerformInteraction` with voice commands, and notifying SDL Core when a voice recognition session begins and ends.
  * `TTS` - Interface responsible for RPC events and information related to text to speech capabilities. Functionality includes speaking text to users, cancelling spoken text, and notifying SDL Core when a text to speech session begins and ends.
  * `Navigation` - Interface responsible for RPC events and information related to navigation, such as audio and video streaming or interacting with the embedded navigation system by updating way points and the turn list. Includes `StartStream` and `GetWayPoints`.
  * `VehicleInfo` - Interface responsible for RPC events and information related to vehicle data. Functionality includes retrieving the current diagnostic codes and messages, and reading vehicle type and data.
  * `RC` - Interface responsible for RPC events and information related to the [Remote Control Feature](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0065-remote-control.md). This includes interacting with interior vehicle data such as seat, light, or radio settings within the vehicle.
  * `AppService` - Interface responsible for RPC events and information related to the [App Services Feature](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0167-app-services.md). This includes publishing and activating an app service, getting app service data, performing an app service interaction, and getting app service consent or records.

### Response

SDL provides a JSON Response

| Key | Value Info   |
| :------------- | :------------- |
| id      | The value from the corresponding request      |
| result | Value of id multiplied by 10. HMI can treat this as a successful registration|

Example Response:
``` json
{
  "id": 100,
  "jsonrpc": "2.0",
  "result": 1000
}
```

## Component Readiness Requests 

Once the components are registered, the HMI must notify SDL Core that it is ready to begin further communication using the [BasicCommunication.OnReady](https://smartdevicelink.com/en/guides/hmi/basiccommunication/onready/) notification.

Upon receipt of the OnReady notification, SDL Core will begin checking the availability of the different HMI components via a chain of requests:

  * `UI.IsReady` - The display availability
  * `VR.IsReady` - The voice recognition module availability
  * `TTS.IsReady` - The text to speech module availability
  * `Navigation.IsReady` - Navigation engine availability
  * `VehicleInfo.IsReady` - Indicates whether vehicle information can be collected and provided
  * `RC.IsReady` - Indicates whether vehicle RC modules are present and ready to communicate with SDL Core

!!! note

In the case of a WebSocket connection, RPCs to each of the components are sent within a separate WebSocket connection.

!!!

|||
IsReady Sequence
![IsReady Sequence](./assets/IsReadySequence.png)
|||

!!! info

If the response to any of the component `IsReady` requests contains `{"available": false}`, SDL Core will no longer communicate with that component.

!!!

## Respond to BasicCommunication.GetSystemInfo

Communicating the current version of the HMI integration (CCPU) is needed for SDL Core to know when to request an update to the HMI's capabilities that may have changed since the previous software version. Core will not mark the HMI as cooperating until this response is sent by the HMI.

Example Response:

```json
{
  "jsonrpc": "2.0",
  "id": rpc.id,
  "result": {
    "method": "BasicCommunication.GetSystemInfo",
    "code": 0,
    "ccpu_version": "0.0.1",
    "language": "EN-US",
    "wersCountryCode": "WAEGB",
  }
}
```
## Registering for Notifications
The HMI must also register for notifications individually using the following RPC format.

```json
{
  "jsonrpc": "2.0",
  "id": -1,
  "method": "MB.subscribeTo",
  "params": {
    "propertyName": <NotificationName>
  }
}
```

"propertyName" is the name of the notification the HMI will receive from Core. Some examples include:

  * Buttons.OnButtonSubscription
  * BasicCommunication.OnAppRegistered
  * BasicCommunication.OnAppUnregistered
  * Navigation.OnVideoDataStreaming
  * SDL.OnStatusUpdate

Core's MessageBroker will not route notifications to the HMI unless the notifications are subscribed to.

!!! must

The HMI must:
  * Register its components
  * Send the OnReady notification
  * Respond to each of the `IsReady` RPCs
  * Register for the notifications it would like to receive

The above steps should only occur once per life cycle of SDL Core

!!!

## Communicating with SDL Core

This section describes the message structure for communication between your HMI and SDL Core.

From this point forward the actors for exchanging messages will be considered:
  - **Client** - can send requests and notifications
  - **Server** - can provide responses to requests from a Client and send notifications

## Request
An RPC call is represented by sending a Request object to a Server. The Request object has the following properties

| Property | Description    |
| :------------- | :------------- |
| id       | An identifier established by the Client. This value must be of unsigned int type in the frames of communication between your HMI and SDL Core. The value should never be null. If "id" is not included the message is assumed to be a notification and the receiver should not respond.|
| jsonrpc | A string specifying the version of JSON RPC protocol being used. Must be exactly **"2.0"** currently in all versions of SDL Core.|
| method | A String containing the information of the method to be invoked. The format is `[componentName].[methodName]`.|
| params | A structured object that holds the parameter values to be used during the invocation of the method. This property may be omitted.|

### Example Requests
#### Request with no Parameters
```json
{
  "id": 125,
  "jsonrpc": "2.0",
  "method": "Buttons.GetCapabilities"
}
```
#### Request with Parameters
```json
{
  "id": 92,
  "jsonrpc": "2.0",
  "method": "UI.Alert",
  "params": {
    "alertStrings": [
      {
        "fieldName": "alertText1",
        "fieldText": "WARNING"
      },
      {
        "fieldName": "alertText2",
        "fieldText": "Adverse Weather Conditions Ahead"
      }
    ],
    "duration": 4000,
    "softButtons": [
      {
        "type": "TEXT",
        "text": "OK",
        "softButtonID": 697,
        "systemAction": "STEAL_FOCUS"
      }
    ],
    "appID": 8218
  }
}
```

## Notification
A notification is a Request object without an `id` property. For all the other properties, see the Request section above.

The receiver should not reply to a notification, i.e. no response object needs to be returned to the client upon receipt of a notification.

### Example Notifications
#### Notification with no Parameters
```json
{
  "jsonrpc": "2.0",
  "method": "UI.OnReady"
}
```
#### Notifications with Parameters
```json
{
  "jsonrpc": "2.0",
  "method": "BasicCommunication.OnAppActivated",
  "params": {
    "appID": 6578
  }
}

{
  "jsonrpc": "2.0",
  "method": "Buttons.OnButtonPress",
  "params": {
    "mode": "SHORT",
    "name": "OK"
  }
}
```

## Response
On receipt of a request message, the server must reply with a Response. The Response is expressed as a single JSON Object with the following properties.

!!! must

An RPC must be sent in result format for its parameters to be passed to mobile.

!!!

| Property | Description    |
| :------------- | :------------- |
| id      | Required property which must be the same as the value of the associated request object. If there was an error in detecting the id in the request object, this value must be null.  |
| jsonrpc | Must be exactly **"2.0"**|
| result | The result property must contain a `method` field which is the same as the corresponding request and a corresponding [result code](https://smartdevicelink.com/en/guides/hmi/common/enums/#result) should be sent in the result property. The result property may also include additional properties as defined in the [HMI API](https://github.com/smartdevicelink/sdl_core/blob/master/src/components/interfaces/HMI_API.xml).|

### Example Responses
#### Response with no Parameters
```json
{
  "id": 167,
  "jsonrpc": "2.0",
  "result": {
    "code": 0,
    "method": "UI.Alert"
  }
}
```
#### Response with Parameters
```json
{
  "id": 125,
  "jsonrpc": "2.0",
  "result": {
    "capabilities" : [
      {
        "longPressAvailable" : true,
        "name" : "PRESET_0",
        "shortPressAvailable" : true,
        "upDownAvailable" : true
      },
      {
        "longPressAvailable" : true,
        "name" : "TUNEDOWN",
        "shortPressAvailable" : true,
        "upDownAvailable" : true
      }
    ],
    "presetBankCapabilities": {
      "onScreenPresetsAvailable" : true
    },
    "code" : 0,
    "method" : "Buttons.GetCapabilities"
  }
}
```

## Error Response

!!! must

An RPC must be sent in error format for its message to be passed to mobile.

!!!

The error object has the following members:

| Property | Description     |
| :------------- | :------------- |
| id       | Required to be the same as the value of "id" in the corresponding Request object. If there was an error in detecting the id of the request object, then this property must be null.   |
| jsonrpc| Must be exactly "2.0"|
| error | The error field must contain a `code` field with the [result code](https://smartdevicelink.com/en/guides/hmi/common/enums/#result) value that indicates the error type that occurred, a `data` field with the `method` from the original request, and optionally a `message` field containing the string that provides a short description of the error.|

### Examples
#### Response with Error
```json
{
  "id": 103,
  "jsonrpc": "2.0",
  "error": {
    "code": 13,
    "message": "One of the provided IDs is not valid",
    "data": {
      "method": "VehicleInfo.GetDTCs"
    }
  }
}
```

#### Response with Warnings and Message
```json
{
  "id": 103,
  "jsonrpc": "2.0",
  "error": {
    "code": 21,
    "message": "Requested image was not found.",
    "data": {
      "method": "UI.Alert"
    }
  }
}
```

## Required Get Capability Responses

As of SDL Core 7.0, SDL Core has the ability to cache certain HMI capabilities and restore them each ignition cycle. On the first time SDL Core is started, or when the HMI's CCPU version changes, SDL Core will request the following messages to the HMI:

- UI.GetLanguage
- UI.GetSupportedLanguage
- UI.GetCapabilities
- RC.GetCapabilities
- VR.GetLanguage
- VR.GetSupportedLanguages
- VR.GetCapabilities
- TTS.GetLanguage
- TTS.GetSupportedLanguages
- TTS.GetCapabilities
- Buttons.GetCapabilities
- VehicleInfo.GetVehicleType

!!! NOTE

If your HMI implementation registers a component (UI, RC, VR, etc), the HMI must respond to the applicable capability requests from Core.

!!!

Greater detail about each of these HMI RPCs can be found in the [HMI API Reference Documentation](https://smartdevicelink.com/en/docs/hmi/master/overview/).
