## 2.  Case Background

### 2.1. System Context, Mission and Scope

ATF is a C++/Lua framework for SDL automated black-box testing.
It provides high- and low-level API for mobile applications and/or HMIs emulation.

ATF *core* lua-scripts provides API for SDL easy manipulation:
  - Start and stop SDL
  - Emulation websocket HMI, HMI interfaces registration and subscription
  - Emulation TCP mobile connection and FORD protocol session establishing
  - Expectation HMI and mobile RPC from SDL
  - Expectation extensions with setting up timeouts, call-chains and fields validation
  - SDL messages auto-validation 

### 2.2. Product Stakeholders

Actors are stakeholders that interact with product directly.

| Stakeholder Name          | Actor (Yes/No) | Concern  |
|---------------------------|----------------|----------|
| Ford Company              | No             | Get the ATF system with enough quality and functionality that fulfill their goals |
| SDL Automation Test teams | Yes            | Get the ATF system with enough functionality for SDL testing coverage |
| PM / Architect / Analyst  | No             | Use Customer Requirements Specification |
| Developers                | No             | Construct and deploy the system from specifications |
| ATF Test team             | No             | Test the system to ensure that it is suitable for use |

### 2.3. Business Goals

ATF system allows to automatize SDL regression testing and decreasing functional end regression costs.
ATF with a smoke tests scripts suite provide [Continuous testing](https://en.wikipedia.org/wiki/Continuous_testing) and [Continuous delivery](https://en.wikipedia.org/wiki/Continuous_delivery) for SDL open-source developers and integrators.

### 2.4. Significant Driving Requirements

The requirements are listed in the table below and ordered by descending of their significance from architectural solution point of view.

| \# | **Driving Requirement Description** |
|----|-------------------------------------|
| 1. | ATF has to be POSIX-compliant to be easily ported on all POSIX standardized OSs. |
| 2. | Script language need to be used as a main-tool for simplification. |
| 3. | ATF shall provide a High-level API for SDL manipulation. |
