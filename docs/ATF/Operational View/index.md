## 4.10. Operational View

This view describes how the architecture provides the ability for operation/support teams to monitor and manage the system. To make system more flexible and to support different platforms, SW provides a configuration and logging components, which are able to change system behavior according to settings defined in smartDeviceLink.ini file and to diagnostic.

#### ATF Configuration
ATF provides default *config.lua* script specifies the desirable system behavior on different platforms and provides settings parameters for each functional component or functionality:

  - Mobile and HMI transports connection
  - Protocol, Connection
  - Path to SDL binary, HMI and Mobile interfaces
  - SDL-related ATF behavior
  - Reporting parameters
  - List of application and they registration parameters

For further information with a list of all available parameters please refer to [*config.lua file*](https://github.com/smartdevicelink/sdl_atf/blob/master/modules/config.lua).

#### Logging 
ATF logging system provides following functionality:

- Logging ATF input and output data
- Storing SDL Core logs with a TCP SDL Core logger
- Testing report with Test Cases results