# SDL Logger

By default, SDL Core uses the [log4cxx](https://logging.apache.org/log4cxx/latest_stable/) framework for logging.

By implementing logger abstraction, SDL now provides the capability to replace the log4cxx logger with any other logging package-such as boost or syslog.

|||
High Level Design
![Logger](./assets/high_level_design.png)
|||


!!! NOTE
All components have access to the `Logger` interface and use it for logging.
!!!

## Logger Interface 

Logger macros use the `Logger` interface for sending messages to the External Logger.
The `Logger` interface contains only methods required by any SDL component to perform logging :

 * instance() - singleton access
 * PushLog(LogMessage)
 * IsEnabledFor(LogLevel)
 * DeInit()
 * Flush()
 * InitLoggerSettings(LoggerSettings)
 * InitFlushLogsTimePoint(TimePoint) 


## Logger Implementation

`Logger` interface is implemented by `LoggerImpl`. 

`LoggerImpl` uses the message loop thread to proxy log messages to a third party (external) logger.
`LoggerImpl` owns `ThirdPartyLoggerInterface` and controls it's lifetime. 
`LoggerImpl` provides implementation of the singleton pattern.


### Message loop thread in SDLLogger

The message loop thread is needed to avoid significant performance degradation at run time as logging calls are blocking calls and might take a significant amount of time. `LoggerImpl::PushLog` is a non-blocking call. It will put the log message into the queue and returns immediately.


If `ThirdPartyLoggerInterface` supports non blocking threaded logging, minor changes in `LoggerImpl` can be made with `use_message_loop_thread = false`.

### Configurable time before shutdown

To prevent missing logs after SDL shutdown, after receiving IGNITION_OFF signal SDL dumps all logs into the file system before shutdown. To control logs flushing process SDL logger has additional ini file options:

 * Option to flush log messages before shutdown.(using `LoggerImpl::Flush` function)
 * Option that specifies maximum time before shutdown.

Logger recieves shutdown settings via `LoggerImpl::InitLoggerSettings(LoggerSettings)`

## Logger singleton 

`Logger` is the only singleton class in SDL. The singleton pattern is required to access the logger instance from any component.

!!! NOTE
`Logger::instance()` provides singleton by `Logger` interface. So SDL components do not have information about the logger implementation and the specific external logger.
!!!

### Logger singleton with plugins 

SDL plugins are shared libraries, so the `Logger` singleton could not be implemented with a Mayers singleton. A Mayers singleton would create an SDL logger instance for each plugin.

The idea is to pass a singleton pointer to each plugin during creation so that plugins can initialize the Logger::instance pointer with the instance received from SDL core.


#### Singleton Instance implementation
```cpp
// ilogger.h
static Logger& instance(Logger* pre_init = nullptr);

...
// logger_impl.cc
Logger& Logger::instance(Logger* pre_init) {
  static Logger* instance_ = nullptr;
  if (pre_init) {
    assert(instance_ == nullptr);
    instance_ = pre_init;
  }
  assert(instance_);
  return *instance_;
}
```

`pre_init` is `nullptr` by default, so all components will access `instance_` static pointer for logging. 
The `main()` function will need to create a `LoggerImpl` object and call `Logger::instance(logger implementation object)`;

#### Plugin implementation
```cpp 
extern "C" PluginType* Create(Logger* logger_singleton_instance) {
  Logger::instance(logger_instance);
  return new PluginType();
}
```


SDL Core will pass a pointer to the logger singleton to the plugin so that the plugin shared library can initialize `Logger::instance` with the same pointer as the core portion.

## Logger detailed design

Each source file creates `logger_` variable via macro `SDL_CREATE_LOG_VARIABLE`. 
This variable is actually a string with the component name of the logger.
Some logger implementations (like log4cxx) may have separate severity or destination rules for each component. 


SDL implements all info required for log message :

 * LogLevel enum
 * Location info struct : location in the code
 * TimePoint 


|||
Detailed Design
![Logger in details](./assets/detailed_logger_design.png)
|||


### LoggerInitializer interface 

`LoggerInitializer` specifies the interface required for `main()` to initialize the logger but is not required for any other SDL components.

`LoggerInitializer::Init` takes the third party logger implementation as an argument. 
 - `Init(std::unique_ptr<ThirdPartyLoggerInterface>&& third_party)`


### ThirdPartyLogger interface

`ThirdPartyLoggerInterface` describes interfaces that should be implemented by the external logger adapter. 

This interface should be inherited by external logger implementations. 


## Implementing another logger 


To use another (not log4cxx) logger, you should: 

 * Create a class which inherits from the `ThirdPartyLoggerInterface` class 

```cpp
AnotherOneLoggerImpl : ThirdPartyLoggerInterface {
  void Init() override;
  void DeInit() override;
  void IsEnabledFor(LogLevel) override;
  void PushLog(const LogMessage& log_message) override;

  void SomeCustomMethod(parameters);
}
```


 * Create an instance of the third party logger implementation(`AnotherOneLoggerImpl`) in `main()` and set it up for `LoggerImpl`.
!!! NOTE
`Logger::instance` does not own the logger instance. The `main` function is responsible for the `sdl_logger_instance_` life-cycle.
!!!


```cpp
// main.cpp
int main(argc, argv) {
	auto external_logger_ = std::make_unique<AnotherOneLoggerImpl>();
	external_logger_->SomeCustomMethod(argv);
	auto sdl_logger_instance_ = new LoggerImpl(std::move(external_logger_));
	Logger::instance(sdl_logger_instance_);  
	sdl_logger_instance_->Init(std::move(external_logger_));
  // Futher application code may use Logger::instance() for logging 
	
	delete sdl_logger_instance_;
}

```
