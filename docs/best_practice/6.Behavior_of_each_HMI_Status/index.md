# 6. Behavior of each HMI Status

## 1. Overview
This chapter provides information about the definition of the HMI Status (Level) and the behavior of SDL Apps by HMI status.
The HU controls the status of display and playback audio by notifying the HMI Level to the mobile.

## 2. Background/Purpose/Reason for Standardization
It is difficult to grasp an overall understanding regarding the HMI Level, since the description of it is included in the related RPC description in the SDL official documents.
Therefore, there is a possibilty that differences may occur depending on the behavior of each HU.
Hence, the purpose of this document is to share information cultivated through the development of SDL compatible HU in Toyota, in order to be able to contribute to the SDL Ecosystem.

## 3. Function Details
### 3.1. Definition of HMI Level and Transition Rule
The definition of behavior and state of transition for each HMI Level are not described in the documents, and the logic is only written in the source code.
Therefore, the definition of behavior and state of transition of each HMI level are shown in the following tables.

**Table1.** Definition of each HMI Level

|<div align="center"> HMI Level </div>|<div align="center"> Definition </div>|
|:---:|:---|
| FULL | The SDL App state is "FULL".<br>An SDL App with this state is displayed on full screen.<br>The SDL App can also operate interactively with other HMI modules(such as UI, VR, TT, Audio system, etc). |
| LIMITED | The SDL App state is "LIMITED".<br>A part of the function or display is limited (Currently, the "Limited" state is only applied to Navigation and Media Apps, for example, SDL App which AppHMIType is "NAVIGATION", "PROJECTION" and "MEDIA". |
| BACKGROUND | The SDL App state is "BACKGROUND".<br>The user has launched the SDL App once, but it is not currently displayed.<br>SDL App in this state can send a part of RPC to the HMI that follows the Policy Table rule. |
| NONE | The SDL App state is "NONE".<br>The user has not launched the SDL App before, or the user has quit the SDL App.<br>SDL App in this state cannot communicate with the HMI. |

**Table2.** Status Transition Rule of HMI Level

|<div align="center"> Status Transition Rule </div>|
|:---|
| If the SDL App HMI Level is FULL", the behavior of other SDL Apps is as follows:<ol>- All SDL Apps except SDL Media/Video App will be BACKGROUND.<br>- All SDL Media/Video Apps(such as NAVIGATION, VC, MEDIA and PROJECTION) will be LIMITED.<br>- SDL Apps which AppHMIType are same will be BACKGROUND.</ol> |
| If the SDL App HMI Level is "LIMITED", the behavior of other SDL Apps is as follows:<ol>- All SDL Apps except SDL Media/Video Apps will keep the current status.<br>- SDL Apps which AppHMIType are different will keep the current status.<br>- SDL Apps which AppHMIType are same will be BACKGROUND.</ol> |
| If the SDL App HMI Level is "BACKGROUND", the behavior of other SDL Apps is as follows:<ol>- All SDL Apps will keep the current status.</ol> |

### 3.2. Status Transition Diagram
In Figure1 below shows the diagram of status transition based on the current status transition rule.
Also, Table3 below shows the various status transition and the expected behavior related to various triggers.

<div align="center">

![figuer1_status_transition_diagram.png](./assets/figuer1_status_transition_diagram.png)
**Figuer1.** Status Transition Diagram
</div>

**Table3.** Various Status Transition and Expected behavior related to Various Trigger
![table3_various_status_transition_and_expected_behavior_related_to_various_trigger.png](./assets/table3_various_status_transition_and_expected_behavior_related_to_various_trigger.png)

###  3.3. Competition of Video Streaming/Audio Streaming
Other than the HMI level status, SDL has also a status for VideoStreaming/AudioStreaming which can be controlled.
The following tables below show the rules of status change, when the user switches SDL App; VideoStreaming in Table4 and AudioStreaming in Table5.

**Table4.** Matrix table of Video Stream status

<table>
  <tr>
    <th colspan="2" rowspan="2">  </th>
    <th align="center" colspan="3"> 2nd launched </th>
  </tr>
  <tr>
    <th align="center"> NAVIGATION </th>
    <th align="center"> PROJECTION </th>
    <th align="center"> Other </th>
  </tr>
  <tr>
    <td align="left" rowspan="3"><b> 1st <br>launched </b></td>
    <td align="center"><b> NAVIGATION </b></td>
    <td align="left"> 1stNAVIGATION:NS<br>2ndNAVIGATION:S </td>
    <td align="left"> 1stNAVIGATION:NS<br>2ndPROJECTION:S </td>
    <td align="left"> 1stNAVIGATION:S<br>2ndOther:NS </td>
  </tr>
  <tr>
    <td align="center"><b> PROJECTION </b></td>
    <td align="left"> 1stPROJECTION:NS<br>2ndNAVIGATION:S </td>
    <td align="left"> 1stPROJECTION:NS<br>2ndPROJECTION:S </td>
    <td align="left"> 1stPROJECTION:S<br>2ndOther:NS </td>
  </tr>
  <tr>
    <td align="center"><b> Other </b></td>
    <td align="left"> 1stOther:NS<br>2ndNAVIGATION:S </td>
    <td align="left"> 1stOther:NS<br>2ndPROJECTION:S </td>
    <td align="left"> 1stOther:NS<br>2ndOther:S </td>
  </tr>
</table>
* S : STREAMABLE, NS : NOT_STREAMABLE<br><br>

**Table5.** Matrix table for Audio Streaming status

<table>
  <tr>
    <th colspan="2" rowspan="2">  </th>
    <th align="center" colspan="4"> 2nd launched </th>
  </tr>
  <tr>
    <th align="center"> NAVIGATION </th>
    <th align="center"> PROJECTION </th>
    <th align="center"> IsMediaApp </th>
    <th align="center"> Other </th>
  </tr>
  <tr>
    <td align="left" rowspan="4"><b> 1st <br>launched </b></td>
    <td align="center"><b> NAVIGATION </b></td>
    <td align="left"> 1stNAVIGATION:NA<br>2ndNAVIGATION:A </td>
    <td align="left"> 1stNAVIGATION:A<br>2ndPROJECTION:NA </td>
    <td align="left"> 1stNAVIGATION:A<br>2ndIsMediaApp:NA </td>
    <td align="left"> 1stNAVIGATION:A<br>2ndOther:NA </td>
  </tr>
  <tr>
    <td align="center"><b> PROJECTION </b></td>
    <td align="left"> 1stPROJECTION:A<br>2ndNAVIGATION:NA </td>
    <td align="left"> 1stPROJECTION:NA<br>2ndPROJECTION:A </td>
    <td align="left"> 1stPROJECTION:A<br>2ndIsMediaApp:NA </td>
    <td align="left"> 1stPROJECTION:A<br>2ndOther:NA </td>
  </tr>
  <tr>
    <td align="center"><b> IsMediaApp </b></td>
    <td align="left"> 1stIsMediaApp:A<br>2ndNAVIGATION:NA </td>
    <td align="left"> 1stIsMediaApp:A<br>2ndPROJECTION:NA </td>
    <td align="left"> 1stIsMediaApp:NA<br>2ndIsMediaApp:A </td>
    <td align="left"> 1stIsMediaApp:A<br>2ndOther:NA </td>
  </tr>
  <tr>
    <td align="center"><b> Other </b></td>
    <td align="left"> 1stOther:NA<br>2ndNAVIGATION:A </td>
    <td align="left"> 1stOther:NA<br>2ndPROJECTION:A </td>
    <td align="left"> 1stOther:NA<br>2ndIsMediaApp:A </td>
    <td align="left"> 1stOther:NA<br>2ndOther:A </td>
  </tr>
</table>
* A : AUDIBLE, NA : NOT_AUDIBLE