## 4.5. Data View

The Data View shows relations between separated data types and actors that perform information processing in the system. It depicts contents of saved information and also visualizes information sources, processors and destination.

The following Diagram shows relations between separated data types and actors that perform information processing in the SmartDeviceLink.

|||
##### Data flow diagram
![Data flow](./assets/image21.png)
|||

***Elements description*** 

#### RawMessage
  - *Summary:*
    - Stores raw data with connection identifier. 
  - *Usage:*
    - Data primitive in *Transport* *Manager*
    - Used by *Protocol* *Handler* as a transport layer income data, connection\_key identifies physical connection
    - Used by *Protocol* *Handler* as a business layer outcome data, connection\_key identifies unique session
 
#### ProtocolFrame
  - *Summary:*
    - Protocol layer primitive with protocol related information. 
  - *Usage:*
    - Used internally by *Protocol* *Handler* for protocol header information prepossessing

#### SecurityQuery
  - *Summary:*
    - *Security* *Manager* primitive type. 
  - *Usage:*
    - Encapsulates TLS handshake and security error data 
 
#### Message
  - *Summary:*
    - Application Manager RPCs primitive type with function and correlation identifiers. 
  - *Usage:*
    - Internally by Protocol Handler for protocol header information prepossessing
    - As abstraction for *RPCs* transferring by *HMI* *Message Helper* 
 
#### SmartObject
  - *Summary:*
    - SmartObject acts as a union for business-layer data and could handle RPCs data as one hierarchy object.
 
  - *Usage:*
    - Used by *Application* *Manager*, *Commands* and *HMI* *Message Helper* for RPCs data filling
    - RPC's data transferring between business-layer components 
    - *Note*: *SmartObjects* are being validated according to MOBILE\_API.xml and HMI\_API.xml.
 
#### Mobile Command and HMI Command
  - *Summary:*
    - RPCs objects with validation and processing data according to business requirements 
  - *Usage:*
    - *Application* *Manager* prepares *Mobile Requests according to SmartObjects *from transport layer
    - *Mobile Request* prepares *SmartObject* for the next *HMI Request* object and subscribes to answer event
    - *Application* *Manager* prepares *HMI Response according to SmartObjects from HMI layer*
    - *HMI Request* prepares *SmartObject* for the next *HMI Request* object
 
#### JSON::Value
  - *Summary:*
    - Json library abstraction 
  - *Usage:*
    - Used as a primitive for JSON format in HMI transport
 
#### DBUS message
  - *Summary:*
    - DBUS message system abstraction 
  - *Usage:*
    - Used as a primitive for DBUS format in HMI transport 
