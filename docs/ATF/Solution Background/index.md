## 6.  Solution Background

### 6.1. Architecture Design Approach

During the architecture designing the following aspects and rules were primary considered:

1.  **Multi-layer architectural approach**: *Transport*, *Business*, *Protocol*, *Application* and *Data Assess* layers
    1.  Each layer has an own component list and provides related to layer functionality 
        - *Note: In future each layer component shall uses only own or low layer interfaces
2.  Lua Script language was used due to following reasons:
    1. Lua as a lightweight and embeddable scripting language could be easily deployed to customer hardware with a limited physical resources amount.
    2. All existing script base was developed with a Lua.
    3. Lua provides a simple procedural syntax for Use Cases implementation.
3.  The whole  *Business*, *Protocol*, *Application* layers are implemented with Lua
    1. It provides an ability to dynamically extent SDL- and Protocol- related functionality
    2. It allows to cover (SDL Application and Protocol layers components)[https://smartdevicelink.com/en/guides/core/software-architecture-document/view-to-view-relations/#51-component-to-layer]
    2. During **User Script** execution Protocol- and SDL- related functionality could easy replaced with Test-specific implementation.
4.  ATF Core is on event idea.
    1.  ATF provides Event system: publisher and subscriber objects, a mechanism to connect them, and event queue, containing emitted events.
    2.  All internal (in **ATF Core**) asynchronous communication is base on the Event system
    3.  All external (in **User Scripts**) Test Cases results waiting and delay expectation subscription is base on the Event system.
5.  **Qt Framework** was selected due to following reasons:
    1.  Signals/slots mechanism (Qt Framework Meta-Object System) for events model
    2.  Cross-platform WebSocket functionality
    3.  Cross-platform Timers functionality

### 6.2. Requirements Coverage

There are indirect requirements which may impact on Architectural decisions, such as limitation of usage of RAM, ROM, requirements to support specific SDL Core to HMI transport layers. All the requirements of this kind were taken into account while creating Architecture Design.

-  [SmartDeviceLink Protocol specification](https://github.com/smartdevicelink/protocol_spec/blob/master/README.md)
-  [SDL-Core Requirements](https://adc.luxoft.com/confluence/display/APPLINK/SDL-GENIVI+Requirements)
-  [ATF Requirements](https://adc.luxoft.com/confluence/display/APPLINK/%5BATF%5D+User+guide+for+requirements)
  - Note: SDL and ATF requirements are handled Luxoft internally and not delivered to open-source.

### 6.3. Prototyping Results

Architecture prototyping was done to validate architecture on early stages. An evolutional prototyping technique was used. Thus all prototype components were used with non-significant changes and additional features for further development.

### 6.4. Open Questions and Known Issues

List of opened questions and issues is available in sdl_core github repository:
-  <https://github.com/smartdevicelink/sdl_atf/issues>

List of Luxoft to Ford opened question is internally available in Luxoft Jira:
-  <https://adc.luxoft.com/jira/issues/?jql=project=project in (APPLINK, SDLOPEN, FORDUSSDL) AND issuetype = Question AND resolution = Unresolved AND labels = to_discuss AND text ~ "atf" ORDER BY key DESC>
  - Note: This list is handled Luxoft internally and not delivered to open-source.

List of Luxoft internal questions is available in Luxoft Jira:
-  <https://adc.luxoft.com/jira/issues/?jql=project=project in (APPLINK, SDLOPEN, FORDUSSDL) AND issuetype = Question AND resolution = Unresolved AND labels != to_discuss AND text ~ "atf" ORDER BY key DESC>
  - Note: This list is handled Luxoft internally and not delivered to open-source.

### 6.5. Results Analysis

Not applicable, since no quantitative or qualitative analysis was performed.
