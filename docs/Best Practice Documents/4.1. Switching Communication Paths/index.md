# 4.1. Switching Communication Paths

## 1. Overview
This chapter describes the priority rule and status notification to switch connections in case of the system connects a device via multiple paths.

## 2. Background/Purpose/Reason for Standardization
The proposal of switching from BT to USB on the same device, "SDL 00053 - Connectivity via iAP-BT and Transport Switch", has been accepted in the SDL Evolution in the SDL Consortium.
However, the switching from BT to USB is applicable only to the iOS system.
Since the behavior of switching communication paths is not explicitly defined in the SDL standard specification, it is necessary for the OEMs to define it by themselves.
Hence, the purpose of this document is to standardize such cases/issues using the TOYOTA specification, in order to be able to contribute to the SDL Ecosystem.

## 3. Function Details
### 3.1. Specification for Switching Communication Paths
These are the basic rules for specification of switching communication paths:
<br>
- The priority of communication methods: USB > BT
Note that WiFi cannot communicate alone by itself, therefore WiFi is excepted from the priority.
- If the SDL connects multiple terminals (mobiles), then the device that is already connected to the SDL is prioritized.
<br>

Table 1 shows the specifications on how SDL connection path is switched when new comunication path is added during SDL connection.

**Table1.** Table for switching multiple Transport

<table>
  <tr>
    <th colspan="2" rowspan="2"></th>
    <th colspan="3"><div align="center"> Additional Connection Method </div></th>
  </tr>
  <tr>
    <th><div align="center"> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;BT&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </div></th>
    <th><div align="center"> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;USB&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </div></th>
    <th><div align="center"> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;WiFi&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </div></th>
  </tr>
  <tr>
    <td rowspan="4"><div align="left"><b> Current <br>Connection </b></div></td>
    <td><div align="center"><b> BT </b></div></td>
    <td><div align="center"> - </div></td>
    <td><div align="center"> USB<br>*1 </div></td>
    <td><div align="center"> BT + WiFi<br>*2 </div></td>
  </tr>
  <tr>
    <td><div align="center"><b> USB </b></div></td>
    <td><div align="center"> USB<br>*3 </div></td>
    <td><div align="center"> - </div></td>
    <td><div align="center"> USB<br>*3 </div></td>
  </tr>
  <tr>
    <td><div align="center"><b> WiFi </b></div></td>
    <td><div align="center"> BT + WiFi </div></td>
    <td><div align="center"> USB </div></td>
    <td><div align="center"> - </div></td>
  </tr>
  <tr>
    <td><div align="center"><b> BT + WiFi </b></div></td>
    <td><div align="center"> - </div></td>
    <td><div align="center"> USB<br>*4 </div></td>
    <td><div align="center"> - </div></td>
  </tr>
</table>
*1 : If the HMI recognized that current BT and new USB are connected to same device, SDL transport should be switched to USB.<br>
*2 : The SDL App that uses VPM detects WiFi transport when it is launched. If the WiFi transport that is connected to same device as BT connection is found, the SDL App will start VPM via WiFi.<br>
*3 : The USB connection path is prioritized, even if both paths are connected to the same device.<br>
*4 : If the SDL App recognized that current BT and new USB are connected to same device, SDL transport should be switched to USB.<br>

### 3.2. Logic for switching communication paths in iOS
The process of switching communication path from BT to USB
<br>
1. When the switching of transport occurs, the SDL Core starts the timer by setting in timeout value in the configuration file.
2. The HMI caches the HMI Level of current running SDL App. After the SDL App has switched the device, the HMI will perform the following process:<br><br>
      (1) Create the list of SDL Apps which is waitting for re-registering<br>
      (2) Terminate the current BT Transport<br>
      (3) Copy the current BT status to the USB device

3. (3-a) If the timer for switching transport expires, the HMI clears the list of SDL Apps which are waiting for the re-registering. Then, the SDL Core calls `Unregistered()` on the SDL Apps which are not registered within the switching time. Afterwards, the result is notified to the HMI from the SDL Core.<br><br>
(3-b) If the HMI has received the RegisterApp notification and SDL App is included in the list of re-registering before the process for switching transport times out, the HMI returns to the previous HMI Level of that SDL App. And then, the HMI notifies to the mobile that the SDL App was launched succesfully.
<br>

## 4. Differences from SDL standard specification
The specification for switching communication paths is not explicitly defined in the SDLC Official documents.
Therefore, the contents described in Table1 above differ from the existing SDL standard specification.

## 5. Implementation Methods
The system has implemented the SDLC's "SDL 0053- Connectivity via iAP-BT and Transport Switch".

## 6. Impacted Platforms
There is no impact on any platforms.