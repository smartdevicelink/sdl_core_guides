# 1.3. SDL App Icon Caching

## 1. Overview
This chapter describes the caching of SDL App Icon on the SDL menu screen.

## 2. Background/Purpose/Reason for Standardization
Currently, the SDL App Icon display is an SDL standard behavior.
However, since the caching or deletion of SDL App Icon is not explicitly defined in the SDL stardard specification, it is necessary for the OEMs to define it by themselves.
Hence, the purpose of this document is to standardize such cases/issues using the TOYOTA specification, in order to be able to contribute to the SDL Ecosystem.

## 3. Function Details
### 3.1. Function Overview
The HMI caches the SDL App information, such as icon, name, etc, for a number of concurrent connections (max. 50) in the memory.
However, when the new registration occurs for the exceeded number of concurrent connections, the new registration is discarded.
When the "RPC'RegisterAppInterface' is not received within 60 seconds after the primary connection" occurs three times from the same device.
The registered SDL App information is cached, until it is deleted under the specific conditions.
Only one device's SDL App information is cashed in the HMI. Therefore it will be deleted under the specific conditions, for example when the connected device is changed.

### 3.2. Deletion of the SDL App Icon/Name
The information of SDL App icon and name will be deleted if it matches the following conditions below:

<ol>
 (1) When the "RPC'RegisterAppInterface' is not received within 60 seconds after the primary connection." 
<ol>occurs three times from the same device</ol>
 (2) During Initialization (Deletion of personal information)<br>
 (3) During change in the language<br>
 (4) When the SDL App data is deleted from the PolicyTable<br>
 (5) When the Mobile connected via SDL is changed
</ol>

Aside from the conditions mentioned above, the SDL App information is basically cached.

## 4. Differences from SDL standard specification
The caching of the SDL App information, such as icon and name, is not explicitly defined in the SDL standard specification, because it is processed in the HMI.
Therefore, all of the contents describe in "3. Function Details" differ from the existing SDL Standard Specification.

## 5. Sequence Diagrams
The following sequence diagrams show the sequence for conditions (1) to (5) mentioned in the "3.2. Deletion of the SDL App Icon/Name"

<div align="center">

![figure1_occurs_three_times_from_the_same_device.png](./assets/figure1_occurs_three_times_from_the_same_device.png)<br>
**Figure1.** Deletion of SDL App Icon Data(The "RPC'RegisterAppInterface' <br>is not received within 60 seconds after the primary connection." occurs three times from the same device)
<br>
<br>
<br>

![figure2_initialization.png](./assets/figure2_initialization.png)<br>
**Figure2.** Deletion of SDL App Icon Data(Initialization (Deletion of the personal information), Change in the language)
<br>
<br>
<br>

![figure3_deletion_from_the_policytable.png](./assets/figure3_deletion_from_the_policytable.png)<br>
**Figure3.** Deletion of SDL App Icon Data(Deletion from the PolicyTable)
<br>
<br>
<br>

![figure4_mobile_connected_is_changed.png](./assets/figure4_mobile_connected_is_changed.png)<br>
**Figure4.** Deletion of SDL App Icon Data(The Mobile connected via SDL is changed.)

</div>

## 6. Impacted Platforms
Changes impact the following platform/s:
- HMI
