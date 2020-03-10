# General Description

The Multiple Transports feature allows apps connected to SDL Core to start another connection over a different transport for certain services. For example, an app connected over Bluetooth can use WiFi as a Secondary Transport for video streaming. This guide will give an overview of the process which is used to establish a Secondary Transport connection. See [SDL-0141 - Supporting Simultaneous Multiple Transports](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0141-multiple-transports.md) for more details on the original feature proposal.

## Implementation
- After the proxy is connected to Core, it initiates another connection over a different transport.
- Core tells the proxy which transport can be used as Secondary Transport.
- The services that are allowed on the Secondary Transport are specified by Core.

!!! Note

RPC and Hybrid services only run on the Primary Transport

!!!

There are three protocol control frames which are used in the implementation of Multiple Transports

### StartService ACK

Payload Example:
```
{
	...
    "audioServiceTransports" : [1, 2],
    "videoServiceTransports" : [1, 2],
    "secondaryTransports" : ["TCP_WIFI"]
}
```

Core responds to the proxy's `StartService` request with additional parameters `audioServiceTransports`, `videoServiceTransports` and `secondaryTransports`.   

- The `secondaryTransports` parameter contains an array of the allowed Secondary Transports for the current Primary Transport. 
- `audioServiceTransports` and `videoServiceTransports` describe which services are allowed to run on which transports (Primary=1, Secondary=2, or both). The proxy uses this information and starts services only on allowed transports.
- This response is constructed by Core using the configurations defined in the SDL INI file, described in [this guide](../../getting-started/multiple-transports-configuration).
- Since RPC and Hybrid services always run on Primary Transport, only Video and Audio services are configurable.

### TransportEventUpdate

Payload Example:
```
{
    "tcpIpAddress" : "192.168.1.1",
    "tcpPort" : 12345
}
```

Core sends a `TransportEventUpdate` notification to the proxy to provide additional information required to connect over the TCP transport when it is available.

- If the `tcpIpAddress` field is empty, the Secondary Transport is unavailable and the proxy will not send a `RegisterSecondaryTransport` request.

### RegisterSecondaryTransport

Using the information in the `StartService ACK` and `TransportEventUpdate` frames, the proxy sends a `RegisterSecondaryTransport` request over the Secondary Transport with the same session ID as the Primary Transport.  

- If Core sends back a `RegisterSecondaryTransport ACK`, the proxy can start services over the Secondary Transport.

## Operation Examples

|||
Start Service (WiFi as Secondary Transport)
![StartService](./assets/StartService.png)
|||

|||
Start Video/Audio service (Over Secondary Transport)
![StartServiceVideo](./assets/StartServiceVideo.png)
|||
  
|||
Start Video/Audio service (No transport available)
![StartServiceVideo NAK](./assets/StartServiceNAK_Video.png)
|||  

|||
Backwards Compatibility (New Proxy/Old Core)
![Compatibility NewProxy OldCore](./assets/Compatibility_NP_OC.png)
|||  

|||
Backwards Compatibility (Old Proxy/New Core)
![Compatibility OldProxy NewCore](./assets/Compatibility_OP_NC.png)
|||  

|||
TransportEventUpdate (Secondary Transport unavailable)
![TransportEventUpdate](./assets/TransportEventUpdate_Disconnected.png)
|||
