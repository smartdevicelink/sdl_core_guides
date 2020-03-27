# 4.2.WiFi Connection

## 1. Overview
This chapter describes about WiFi connection.

## 2. Background/Purpose/Reason for Standardization
Currently, there is no explicit summary regarding SecondaryTransport connection including WiFi,  because it is still under proposal in the SDL Evolution.
Therefore, the purpose of this document is to summarize the proposal and standardize the issue, in order to be able to contribute to the SDL Ecosystem.

## 3. Function Details
### 3.1. Function overview
WiFi connection does not communicate using PrimaryTransport, rather it is registered as one of the communication method of SecondaryTransport.
If both Primary and Secondary connection were enabled, each will have the following roles.

 -PrimaryTransport : It will be used for the communication of RPC.
 -SecondaryTransport : It will be used for the communication of Data service such as Video streaming/Audio streaming.

WiFi connection will be established between the SDL App and the HU by following the SmartDeviceLink protocol specification (<https://www.smartdevicelink.com/en/guides/sdl-overview-guides/protocol-spec/>). Then, it will be registered as SecondaryTransport.

!!! NOTE
Due to the limitation of iOS specification, WiFi connection will be disabled when the SDL App goes "BACKGROUND" or the mobile screen is locked.
Therefore, WiFi can not be connected as a PrimaryTransport.
!!!

### 3.2. Connection Method
The communication on SecondaryTransport follows the SmartDeviceLink protocol specification.
Below is a list of control frame that is used for SecondaryTransport connection.

**Table1.** Control Frame list

|<div align="center"> No. </div>|<div align="center"> Frame <br>value </div>|<div align="center"> Frame </div>|<div align="center"> Description </div>|
|:---:|:---:|:---|:---|
|<div align="center"> 1 </div>|<div align="center"> 0x01 </div>|<div align="left"> Start Service </div>|<div align="left"> Requests to start specific (type of) service. </div>|
|<div align="center"> 2 </div>|<div align="center"> 0x02 </div>|<div align="left"> Start Service ACK </div>|<div align="left"> Notifies that Start Service has started successfully.<br>`Strat Service ACK` of PrimariyTransport has the information of SecondaryTransport.<br>When the SDL App establish SecondaryTransport session, this informaton will be refered.<br>The information on protocol version is attributed in this frame. </div>|
|<div align="center"> 3 </div>|<div align="center"> 0x03 </div>|<div align="left"> Start Service NAK </div>|<div align="left"> Notifies that Start Service has failed. </div>|
|<div align="center"> 4 </div>|<div align="center"> 0x07 </div>|<div align="left"> Register Secondary Transport </div>|<div align="left"> Request a session that is registered on the PrimaryTransport to use SecondaryTransport.<br>This frame is sent from the proxy to the SDL Core.<br>This frame must only be sent on SecondaryTransport that the session is requesting. </div>|
|<div align="center"> 5 </div>|<div align="center"> 0x08 </div>|<div align="left"> Register Secondary Transport ACK </div>|<div align="left"> Notifies that the session registered to use the requested SecondaryTransport.<br>If the SDL App received this frame, it can send the additional frame on SecondaryTransport.<br>This frame must be sent on the SecondaryTransport, in which the original request is sent(`Register SecondaryTransport`). </div>|
|<div align="center"> 6 </div>|<div align="center"> 0x09 </div>|<div align="left"> Register Secondary Transport NAK </div>|<div align="left"> Notifies that the session is not registered or not able to use the current SecondaryTransport.<br>The SDL App cannot use this SecondaryTransport for other messages.<br>This frame must be sent on the SecondaryTransport, in which the original request is sent(`Register SecondaryTransport`). </div>|
|<div align="center"> 7 </div>|<div align="center"> 0xFD </div>|<div align="left"> Transport Event Update </div>|<div align="left"> Shows the updated information on more than one transport and is sent from the SDL Core to the Proxy.<br>Information such as the TCPIP Address/the TCP Port are sent.<br>Note that this frame should NOT be sent before the SmartDeviceLink Protcol version is determined. </div>|

Next, listed below are the parameter set in the control frame.
About the parameter setting, please refer to the `smartDeviceLink.ini` file in the SDL Core.

**Table2.** Parameters set in Control frame

|<div align="center"> No. </div>|<div align="center"> Parameter </div>|<div align="center"> Description </div>|
|:---:|:---|:---|
|<div align="center"> 1 </div>|<div align="left"> protocolVersion </div>|<div align="left"> Parameter of `Start Service ACK` frame.<br>Shows the version of SmartDeviceLink Protocol specification (Ex. "5.0.0").<br>The newest version of SmartDeviceLink Protocol specification is needed to create the SDL App. </div>|
|<div align="center"> 2 </div>|<div align="left"> secondaryTransports[] </div>|<div align="left"> Parameter of `Start Service ACK` frame.<br>Shows transport type which supports as SecondaryTransport.<br>The proxy starts to set up SecondaryTransport, when Transport Type is notified.<br>If there is no supported Transport Type, the array should be omitted or emptied.<br>Please refer to table3 for the kinds of Transport Type. </div>|
|<div align="center"> 3 </div>|<div align="left"> audioServiceTransports </div>|<div align="left"> Parameter of `Start Service ACK` frame.<br>Shows transports which support transport path for the Audio service.<br>The type is int32 with each of the following meaning below:<br><br> 1 : Audio service is available on PrimaryTransport.<br> 2 : Audio service is available on SecondaryTransport.<br><br>If both Transports (Primary/Secondary) support the Audio service, then it should be in prioritize order and the SDL core will decide which transport will be used.<br>If `Start Service ACK` is for SecondaryTransport, this parameter should not be included.<br>If this parameter is not included in `StartServiceACK` frame, the SDL App must launch the Audio service on PrimaryTransport. </div>|
|<div align="center"> 4 </div>|<div align="left"> videoServiceTransports </div>|<div align="left"> Parameter of `Start Service ACK` frame.<br>Shows transports which support transport path for the Video Projection Mode (VPM) service.<br>The type is int32 with each of the following meaning below:<br><br> 1 : VPM service is available on PrimaryTransport.<br> 2 : VPM service is available on SecondaryTransport.<br><br>If both Transports (Primary/Secondary) support the VPM service, then it should be in prioritize order and the SDL core will decide which transport will be used.<br>SDL App must not launch transport that is not listed in the array.<br>If this parameter is not included in `StartServiceACK` frame, the SDL App must launch the VPM service on PrimaryTransport. </div>|
|<div align="center"> 5 </div>|<div align="left"> tcpIpAddress </div>|<div align="left"> Parameter of `Transport Event Update` frame.<br>Shows the IP Address used to establish a TCP connection.<br>It can be (set as) either IPv4 Address(ex: "192.168.1.1") or IPv6 Address(ex: "fd12:3456:789a::1").<br>If IP address is blank, it indicates that the TCP transport cannot be used. </div>|
|<div align="center"> 6 </div>|<div align="left"> tcpPort </div>|<div align="left"> Parameter of `Transport Event Update` frame.<br>Shows TCP port numbers used to establish a connection using tcpIpAddress.<br>The value must be the same as TCPAdapterPort in the smartDeviceLink.ini file.<br>If this parameter is set, then the value of tcpIpAddress must be included as well. </div>|

!!! NOTE
[] indicates the array type, and able for multiple settings.
!!!

Listed below are Transport Type strings used in the parameter of SecondaryTransport.

**Table3.** TransportType strings list set on SecondaryTransports

|<div align="center"> No. </div>|<div align="center"> TransportType string </div>|<div align="center"> Description </div>|
|:---:|:---|:---|
|<div align="center"> 1 </div>|<div align="left"> IAP_BLUETOOTH </div>|<div align="left"> iPhone Accessary Protocol(iAP) via Bluetooth </div>|
|<div align="center"> 2 </div>|<div align="left"> IAP_USB </div>|<div align="left"> iAP via USB, cannot distinguish between the Host mode and the Device mode </div>|
|<div align="center"> 3 </div>|<div align="left"> IAP_USB_HOST_MODE </div>|<div align="left"> iAP via USB, mobile is running as the Host </div>|
|<div align="center"> 4 </div>|<div align="left"> IAP_USB_DEVICE_MODE </div>|<div align="left"> iAP via USB, mobile is running as the Device </div>|
|<div align="center"> 5 </div>|<div align="left"> IAP_CARPLAY </div>|<div align="left"> iAP via CarPlay wireless </div>|
|<div align="center"> 6 </div>|<div align="left"> SPP_BLUETOOTH </div>|<div align="left"> Blutooth Serial Port Profile(SPP) </div>|
|<div align="center"> 7 </div>|<div align="left"> AOA_USB </div>|<div align="left"> Android Open Accessary(AOA) </div>|
|<div align="center"> 8 </div>|<div align="left"> TCP_WIFI </div>|<div align="left"> TCP connection via WiFi </div>|


### 3.3. Conditions for SecondaryTransport Connection
Using SecondaryTransport connection has the following conditions below:

 - `Transport Event Update` and `Register Secondary Transport` are not sent before the detarmination of SmartDeviceLink protocol version.
 - If PrimaryTransport is disconnected, then SecondaryTransport is stopped too.
 - `Register Secondary Transport` frame is sent after all of the information of SecondaryTransport configuration is determined.
 - (To avoid interference with each other) using WiFi in 5GHz frequency band is recommended, because Bluetooth runs in a 2.4Ghz frequency band.
 - There is no means to terminate SecondaryTransport connection.

### 3.4. WiFi Connection
Listed below are the necessary information (parameter) for WiFi connection.
By setting all of the information, WiFi connection can be used as SecondaryTransport.
For the sequence of WiFi connection, refer to "4. Sequence Diagrams".
Note that WiFi connection cannot be set when the DriverDistraction is ON. Thus, it is set during parking.

**Table4.** WiFi setting Parameter

|<div align="center"> No. </div>|<div align="center"> Parameter </div>|<div align="center"> Description </div>|
|:---:|:---|:---|
|<div align="center"> 1 </div>|<div align="left"> supportWiFiAutoConnect </div>|<div align="left"> Indicates whether the automatic WiFi connection is supported or not, as the parameter of struct `DeviceInfo`.<br> True : Supported<br> False : Not supported </div>|
|<div align="center"> 2 </div>|<div align="left"> WiFiStateInfo </div>|<div align="left"> Shows WiFi Status as the parameter of RPC`GetWiFiStatusInfo(Response)`.<br> WIFI_STATE_DISABLED ： WiFi is disabled.<br> WIFI_STATE_ENABLED ： WiFi is enabled. </div>|
|<div align="center"> 3 </div>|<div align="left"> ssid </div>|<div align="left"> Shows the specific name of WiFi AccessPoint(AP) as the parameter of RPC`GetWiFiStatusInfo(Response)`.<br>Max length is 32 characters, and can use half-width alphanumeric characters. </div>|
|<div align="center"> 4 </div>|<div align="left"> password </div>|<div align="left"> Shows the password to connect WiFi AP as the parameter of RPC`GetWiFiStatusInfo(Response)`.<br>Max length is 100 characters, and can use half-width alphanumeric(upper/lowercase) and partical symbols characters. </div>|
|<div align="center"> 5 </div>|<div align="left"> WiFiSecurityType </div>|<div align="left"> Shows the security method to connect WiFi AP as the parameter of RPC`GetWiFiStatusInfo(Response)`.<br>Listed below are the securityTypes settings that are currently available:<br> WIFI_SECURITY_NONE : No Security<br> WIFI_SECURITY_WEP : Uses WEP method security<br> WIFI_SECURITY_WPA : Uses WPA method security<br> WIFI_SECURITY_WPA2 : Uses WPA2 method Security </div>|

## 4. Sequence Diagrams
Refer to Figure1 for the sequence of SecondaryTransport connection establishment, and Figure2 for the sequence of WiFi connection.

|||
**Figure1.** Establishment sequence of SecondaryTransport connection
![figure1_establishment_of_secondarytransport_connection.png](./assets/figure1_establishment_of_secondarytransport_connection.png)
|||
|||
**Figure2.** WiFi connection sequence
![figure2_wifi_connection.png](./assets/figure2_wifi_connection.png)
|||

## 5. Impacted Platforms
Changes impact the following platform/s:
 - Proxy
 - SDL Core
 - HMI