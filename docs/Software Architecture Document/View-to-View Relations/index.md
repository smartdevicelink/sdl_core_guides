## 5.  View-to-View Relations

Each of the views specified in Section 3 provides a different perspective and design handle on a system, and each is valid and useful in its own right. Although the views give different system perspectives, they are not independent. Elements of one view will be related to elements of other views, and we need to reason about these relations.

### 5.1. Component-to-Layer

The following table is a mapping between the elements in the Component view and the Development view. The relationship shown is *is-implemented-by*, i.e. the layers from the Development view shown at the top of the table are implemented by any selected elements from the Component view, denoted by an "X" in the corresponding cell.

| ***Component / Layer***   | **Application Layer** | **Protocol Layer** | **Transport Layer** | **OS Layer** |
|---------------------------|:---------------------:|:------------------:|:-------------------:|:------------:|
| ***Life Cycle***          | X                     |                    |                     |              |
| ***Config Profile***      | X                     |                    |                     |              |
| ***Application Manager*** | X                     |                    |                     |              |
| ***Commands***            | X                     |                    |                     |              |
| ***Request Controller***  | X                     |                    |                     |              |
| ***App Launch***          | X                     |                    |                     |              |
| ***Resumption***          | X                     |                    |                     |              |
| ***Policy***              | X                     |                    |                     |              |
| ***Media Manager***       | X                     |                    |                     |              |
| ***Protocol Handler***    |                       | X                  |                     |              |
| ***Connection Handler***  |                       | X                  |                     |              |
| ***Security Manager***    |                       | X                  |                     |              |
| ***HMI Message Handler*** |                       |                    | X                   | X            |
| ***Transport Manager***   |                       |                    | X                   |              |
| ***Transport Adapter***   |                       |                    | X                   | X            |
| ***Utils***               |                       |                    |                     | X            |
| ***Component / Layer***   | **Application Layer** | **Protocol Layer** | **Transport Layer** | **OS Layer** |

### 5.2. Data-to-Layer View

The following table is a mapping between the elements in the Data view and the Development view. The relationship shown is *is-implemented-by*, i.e. the layers from the Development view shown at the top of the table are implemented by any selected elements from the Component view, denoted by an "X" in the corresponding cell.

| ***Data / Layer***    | **Application Layer** | **Protocol Layer** | **Transport Layer** | **OS Layer** |
|-----------------------|:---------------------:|:------------------:|:-------------------:|:------------:|
| ***Message***         | X                     |                    |                     |              |
| ***SmartObject***     | X                     |                    |                     |              |
| ***Mobile Command***  | X                     |                    |                     |              |
| ***HMI Command***     | X                     |                    |                     |              |
| ***ProtocolFrame***   |                       | X                  |                     |              |
| ***SecurityQuery***   |                       | X                  |                     |              |
| ***RawMessage***      |                       | X                  | X                   | X            |
| ***JSON::Value***     |                       |                    | X                   | X            |
| ***DBUS message***    |                       |                    | X                   | X            |

