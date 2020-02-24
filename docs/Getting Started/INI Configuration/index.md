
# smartDeviceLink.ini
The ini file, located at `build/src/appMain/smartDeviceLink.ini` after you compile and install SDL, is where runtime options can be configured for your instance of SDL Core. Descriptions for each of these configurations are found in [the file itself](https://github.com/smartdevicelink/sdl_core/blob/master/src/appMain/smartDeviceLink.ini).

## Sections

* `HMI` - Settings relating to the HMI connection, including server and port information.
* `MEDIA MANAGER` - Settings related to media features (audio/video streaming and audio pass thru). Several of these options are described in more detail in the [Audio/Video Streaming Setup guide](https://smartdevicelink.com/en/guides/core/audio-and-video-streaming-setup/).
* `GLOBAL PROPERTIES` - Settings to define default values to set when `ResetGlobalProperties` is sent by a mobile application.
* `FILESYSTEM RESTRICTIONS` - Settings to define limits for file operations by applications in the `NONE` HMI Level.
* `AppInfo` - Settings for where to store application info for resumption purposes.
* `Security Manager` - Only used when built with ENABLE_SECURITY=ON. Settings to define how Core establishes secure services, as well as which services need to be protected.
* `Policy` - Options for policy table storage and usage.
* `TransportManager` - Configuration options for each transport adapter, including system information to be sent to SDL applications.
* `CloudAppConnections` - Only used when built with BUILD_CLOUD_APP_SUPPORT=ON. Settings for connecting to cloud applications.
* `ProtocolHandler` - SDL Protocol-level options, including the protocol version used by Core.
* `SDL5` - SDL Protocol options which were introduced with protocol version 5, allows for specifying invidividual MTUs by service type.
* `ApplicationManager` - Miscellaneous settings related to application handling.
* `Resumption` - Options regarding application resumption data storage and handling.
* `TransportRequiredForResumption` - Options for restricting HMI level resumption based on app type and transport (defined in [SDL-0149](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0149-mt-registration-limitation.md)).
* `LowBandwidthTransportResumptionLevel` - Extended options for restricting resumption, where exceptions can be defined for the rules in `TransportRequiredForResumption` (defined in [SDL-0149](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0149-mt-registration-limitation.md)).
* `MultipleTransports` - Settings related to the Multiple Transports feature, allowing an application to connect over two transports at the same time (defined in [SDL-0141](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0141-multiple-transports.md)).
* `ServicesMap` - Settings for restricting Audio and Video services by transport, to be used in conjunction with the `MultipleTransports` section (defined in [SDL-0141](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0141-multiple-transports.md)).
* `AppServices` - Configuration options related to the app services feature (defined in [SDL-0167](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0167-app-services.md)).
* `RCModuleConsent` - Settings regarding storage of RC module consent records.
