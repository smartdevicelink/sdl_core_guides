## 4.8. Development View

### 4.8.1. Implementation Technologies

- C++11 language is selected as a programming language for ATF Core binary as a OS and CPU architecture independent.
- Qt selected as a cross-platform Framework for ATF utility support: Signal-Slot metasystem, Websocket implementation, timers and so on
  - *Note:* Version Qt 5.3 is required for WebScoket transport support.
- Lua language selected as a lightweight, embeddable scripting language for ATF Components and **User Scripts* implementation

### 4.8.2. Modules and Code Base Organization

Development view organizes ATF components into a separate directories:

- **src** - C++ source code for ATF C++ Core.
- **modules** - Lua components
  - **modules/protocol_handler** - **Protocol handler** components
  - **modules/atf** - **Utility** components

### 4.8.3. Development Environment and Standards
-   Development and testing environment for Ubuntu 14.04 LTS x32/x64
    -   Debug Environment: Ubuntu 14.04 LTS x32/x64, Qt 5.3style
    -   Compiler: GCC 4.9.3 (OS Ubuntu), Lua 5.2
    -   Build system: qmake (from Qt 5.3), Cmake 2.8.12.2
-   Development and testing environment for SDL Windows x64:
-   Requirements Management system: LuxProject (JIRA, Confluence)
-   Source Control System: GitHub
-   Issue Tracking System: LuxProject (JIRA)
-   Document Management System: LuxProject (JIRA, Confluence, SVN)
-   С++ Coding style: [*Google C++ Style Guide*](https://google.github.io/styleguide/cppguide.html)
-   Lua Coding style: [*Lua Style Guide*](http://lua-users.org/wiki/LuaStyleGuide)
