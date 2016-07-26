### 4.10. Operational View

This view describes how the architecture provides the ability for operation/support teams to monitor and manage the system. To make system more flexible and to support different platforms, SW provides a configuration and logging components, which are able to change system behavior according to settings defined in smartDeviceLink.ini file and to diagnostic 

***Elements description***

#### Configuration
  - *Config Profile* component specifies the desirable system behavior on different platforms and provides settings parameters for each functional component or functionality:
    - Mobile and HMI transports connection
    - Protocol, Connection, Security
    - Policy, Resumption
    - File system restrictions
    - Global properties like HelpPrompt, TimeoutPrompt, HelpTitle, HelpCommand
    - Default Timeout for mobile application commands
    - Desirable location of the system data (log files, persistence data, temporary data) 
  - For further information with a list of all available parameters please refer to chapter "15.1 SDL’s configuration file structure" of [HMI Guideline](TODO(EZamakhov):add cross-link) or [*smartDeviceLink .ini file*](https://github.com/smartdevicelink/sdl_core/blob/master/src/appMain/smartDeviceLink.ini).

#### Diagnostics
  - SmartDeviceLink system provides diagnostics messages log file with following types of messages:
    - ***FATAL*** message indicates abnormal problem related to external subsystems contract violation or SDL implementation issues. It indicates some critical issue and all SDL behaviors is undefined after this message.
    - ***ERROR*** message shows, that the problem occurred and SDL has not accomplished some internal or API activities. Error is successfully handled by SDL, but notifies about some business logic's flow breakdown. 
    - ***WARNING*** message warns against uncommon or rare flow. This message indicates handling some expected by SDL issue according to specified requirements.
    - ***INFO*** informs SDL user, integrators or support engineer about the component high-level activity success.
    - ***DEBUG*** and ***TRACE*** messages contain debug information for software engineer diagnostics and deep issues analysis.
    [comment]: # (TODO(EZamakhov): Move Configuration levels article to Wiki or directly to SAD - APPLINK-26781
  - For further information about logger configuration and usage please refer [related article](https://adc.luxoft.com/confluence/display/APPLINK/Logger+levels+and+property+files+usage).