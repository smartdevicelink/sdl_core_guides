## 6.  Solution Background

### 6.1. Architecture Design Approach

During the architecture designing the following aspects and rules were primary considered:

1.  **Three-layer architectural approach**: Transport (low), Protocol (middle), Application (high) layer.
    1.  Each layer component uses only own or low layer interfaces
    2.  `Observer` design pattern is required for providing information for upper layer components.
2.  **Weak components and classes coupling** for providing SmartDeviceLink Extensibility.
    1.  Providing each component and class functionality with an interface.
    2.  `Facade` design pattern is used for Mobile and HMI transports functionality within one interface.
    3.  `Observer` interface for providing information for upper layer components.
    4.  Binding different layers components is in LifeCycle component responsibility.
3.  **System scalability** for adding new interrogation platform transport. 
    1.  `Adapter` design pattern is used to provide permanent interface to transport layer.
    2.  `Abstract Factory` design pattern is used to create related objects without specifying their concrete classes directly.
    3.  `Command` design pattern is used to treat requests as an object that provides possibility to add new request without existing code modification.
4.  **OS and hardware abstraction** for simplifying portability to non-POSIX-compliant OS.
    1.  `Adapter` pattern is used for preparing the cross-platform interface for thread, timer, synchronization, file and data access functionality.
    2.  For HMI and Mobile transports used `adapter` pattern with a unified interface, which could be reused for new platform- and OS- specific transport API adoption
    3.  OS-related and 3d-party libraries APIs are segregated in Utils component, which available for all SDL layers 
    4.  Utils classes are implemented with `Bridge` design pattern (PIMPL idiom) for avoiding platform and 3d-party libraries dependencies. 
5.  **Asynchronous data and notification handling** for meeting real time restrictions on transport layer and providing vertical scalability.
    1.  Different data-types processing preferable in separate threads.
    2.  For internal data processing components preferable to use `Utils::threads::MessageLoopThread` for the same data objects processing
        -  Asynchronous call result has to be provided with Observers interfaces to callee
    3.  For transport API adapters preferable the own Utils::threads::Thread implementation for meeting realtime restrictions
    4.  SmartDeviceLink logging implemented with `Utils::threads::MessageLoopThread` for avoiding console, file and other appends delay affect on functionality
    5.  For pending large number processing RPCs selected `Controller` design pattern with a limited and configurable count of processing threads
6.  **Resource Acquisition Is Initialization** (RAII, or Scope-based Resource Management) for memory, mutex, files and other resources management.
    1.  `utils::SharedPtr` template class is implemented for memory deallocation.
    2.  `utils::AutoLock` is implemented for mutex acquiring and releasing 
    3.  `utils::ScopeGuard` is implemented for external memory and resource deinitialization.
7.  **Strict heap memory usage** for avoid memory leaks and memory corruption.
    1.  SmartDeviceLink objects aggregation is preferable by reference link storing instead of raw pointer,
        - Note: in case external class life-time guaranty.
    2.  System objects composition is preferable by value or by smart pointer:
        1.  In case of exclusive object handling could `std::auto_ptr` is preferable
        2.  For shared object handling `utils::SharedPtr` is preferable

### 6.2. Requirements Coverage

There are indirect requirements which may impact on Architectural decisions, such as limitation of usage of RAM, ROM, requirements to support specific SDL Core to HMI transport layers. All the requirements of this kind were taken into account while creating Architecture Design. 
-   [*SmartDeviceLink Protocol specification*](https://github.com/smartdevicelink/protocol_spec/blob/master/README.md)

### 6.3. Prototyping Results

Architecture prototyping was done to validate architecture on early stages. An evolutional prototyping technique was used. Thus all prototype components were used with non-significant changes and additional features for further development.

### 6.4. Open Questions and Known Issues

None

### 6.5. Results Analysis

Not applicable, since no quantitative or qualitative analysis was performed.
