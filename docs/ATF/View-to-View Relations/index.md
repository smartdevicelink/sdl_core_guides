## 5.  View-to-View Relations

Each of the views specified in Section 3 provides a different perspective and design handle on a system, and each is valid and useful in its own right. Although the views give different system perspectives, they are not independent. Elements of one view will be related to elements of other views, and we need to reason about these relations.

### 5.1. Component-to-Layer

The following table is a mapping between the elements in the Component view and the Development view. The relationship shown is *is-implemented-by*, i.e. the layers from the Development view shown at the top of the table are implemented by any selected elements from the Component view, denoted by an "X" in the corresponding cell.

| ***Component / Layer***   | **Application Layer** | **Business Layer** | **Protocol Layer**  | **Transport Layer** |**Data Assess Layer**|
|---------------------------|:---------------------:|:------------------:|:-------------------:|:-------------------:|:-------------------:|
| ***Connection Test***     | X                     |                    |                     |                     |                     |
| ***Launch***              | X                     |                    |                     |                     |                     |
| ***Validator***           |                       | X                  |                     |                     |                     |
| ***Test Base***           |                       | X                  |                     |                     |                     |
| ***SDL***                 |                       | X                  |                     |                     |                     |
| ***Mobile Session***      |                       |                    | X                   |                     |                     |
| ***Protocol Handler***    |                       |                    | X                   |                     |                     |
| ***Mobile Connection***   |                       |                    |                     | X                   |                     |
| ***HMI Connection***      |                       |                    |                     | X                   |                     |
| ***Config***              |                       |                    |                     |                     | X                   |
| ***Events***              |                       |                    |                     |                     | X                   |
| ***ATF logger***          |                       |                    |                     |                     | X                   |
| ***Utils***               |                       |                    |                     |                     | X                   |
| ***Component / Layer***   | **Application Layer** | **Business Layer** | **Protocol Layer**  | **Transport Layer** |**Data Assess Layer**|

### 5.2. Data-to-Layer View

The following table is a mapping between the elements in the Data view and the Development view. The relationship shown is *is-implemented-by*, i.e. the layers from the Development view shown at the top of the table are implemented by any selected elements from the Component view, denoted by an "X" in the corresponding cell.

| ***Data / Layer***        | **Application Layer** | **Business Layer** | **Protocol Layer**  | **Transport Layer** |**Data Assess Layer**|
|---------------------------|:---------------------:|:------------------:|:-------------------:|:-------------------:|:-------------------:|
| ***HMI RPC***             | X                     | X                  |                     |                     |                     |
| ***Mobile RPC***          | X                     | X                  |                     |                     |                     |
| ***Json data***           |                       | X                  |                     |                     | X                   |
| ***protocol packet***     |                       |                    | X                   |                     |                     |
| ***Socket raw data***     |                       |                    |                     | X                   |                     |
| ***WebSocket raw data***  |                       |                    |                     | X                   |                     |
| ***Data / Layer***        | **Application Layer** | **Business Layer** | **Protocol Layer**  | **Transport Layer** |**Data Assess Layer**|

