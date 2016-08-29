### 4.9. Operational View

This view describes how the architecture provides the ability for operation/support teams to monitor and manage the system. To make system more flexible and to support different platforms, SW provides a configuration and logging components, which are able to change system behavior according to settings defined in smartDeviceLink.ini file and to diagnostic.

#### SDL Configuration
*Config Profile* component specifies the desirable system behavior on different platforms and provides settings parameters for each functional component or functionality:

  - Mobile and HMI transports connection
  - Protocol, Connection, Security
  - Policy, Resumption
  - File system restrictions
  - Global properties like HelpPrompt, TimeoutPrompt, HelpTitle, HelpCommand
  - Default Timeout for mobile application commands
  - Desirable location of the system data (log files, persistence data, temporary data)

For further information with a list of all available parameters please refer to chapter "15.1 SDL’s configuration file structure" of [HMI Guideline](TODO(EZamakhov):add cross-link) or [*smartDeviceLink .ini file*](https://github.com/smartdevicelink/sdl_core/blob/master/src/appMain/smartDeviceLink.ini).

#### Logging Configuration
SDL logging system (with a log4cxx library for posix build) provides powerful flexibility and allows to configure SDL for development, integrator and user purposes by changing [log4cxx property file](https://github.com/smartdevicelink/sdl_core/blob/master/src/appMain/log4cxx.properties)
Each SDL component can be configured with own:

  - [Logging level output](#diagnostics)
    - *Example:* for user needs using Warning+ level is preferable for all OSm Transport and Protocol layers components.
  - Output source appender
    - SDL (with Log4cxx) can log to the [console, files, remote socket servers, NT Event Loggers, remote UNIX Syslog daemons and others](https://logging.apache.org/log4cxx/latest_stable/apidocs/classlog4cxx_1_1_appender_skeleton.html).
  - own [output log pattern](https://svn.apache.org/repos/asf/logging/site/trunk/docs/log4cxx/apidocs/classlog4cxx_1_1_pattern_layout.html)

For further information about configuration please refer:

  - [log4cxx HowTo](https://logging.apache.org/log4cxx/latest_stable/usage.html)
  - [Configuring loggers](https://logging.apache.org/log4cxx/latest_stable/apidocs/classlog4cxx_1_1_property_configurator.html#ad2a603ef30c78d771335bf3c83f39a6d)

#### Diagnostics
SmartDeviceLink system provides diagnostics messages log file with following types of messages:

  - ***FATAL*** message indicates abnormal problem related to external subsystems contract violation or SDL implementation issues. It indicates some critical issue and all SDL behaviors is undefined after this message.
  - ***ERROR*** message shows, that the problem occurred and SDL has not accomplished some internal or API activities. Error is successfully handled by SDL, but notifies about some business logic's flow breakdown. 
  - ***WARNING*** message warns against uncommon or rare flow. This message indicates handling some expected by SDL issue according to specified requirements.
  - ***INFO*** informs SDL user, integrators or support engineer about the component high-level activity success.
  - ***DEBUG*** and ***TRACE*** messages contain debug information for software engineer diagnostics and deep issues analysis.

For further information about logging levels usage please refer [related article](https://github.com/smartdevicelink/sdl_core/wiki/SDL-Logging-levels).