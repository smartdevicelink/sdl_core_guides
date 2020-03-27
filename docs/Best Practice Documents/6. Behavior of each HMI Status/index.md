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
|<div align="center"> FULL </div>|<div align="left"> The SDL App state is "FULL".<br>An SDL App with this state is displayed on full screen.<br>The SDL App can also operate interactively with other HMI modules(such as UI, VR, TT, Audio system, etc). </div>|
|<div align="center"> LIMITED </div>|<div align="left">  The SDL App state is "LIMITED".<br>A part of the function or display is limited (Currently, the "Limited" state is only applied to Navigation and Media Apps, for example, SDL App which AppHMIType is "NAVIGATION", "PROJECTION" and "MEDIA". </div>|
|<div align="center"> BACKGROUND </div>|<div align="left"> The SDL App state is "BACKGROUND".<br>The user has launched the SDL App once, but it is not currently displayed.<br>SDL App in this state can send a part of RPC to the HMI that follows the Policy Table rule. </div>|
|<div align="center"> NONE </div>|<div align="left"> The SDL App state is "NONE".<br>The user has not launched the SDL App before, or the user has quit the SDL App.<br>SDL App in this state cannot communicate with the HMI. </div>|

**Table2.** Status Transition Rule of HMI Level

|<div align="center"> Status Transition Rule </div>|
|:---|
|<div align="left"> If the SDL App HMI Level is FULL", the behavior of other SDL Apps is as follows:<br><br> - All SDL Apps except SDL Media/Video App will be BACKGROUND.<br> - All SDL Media/Video Apps(such as NAVIGATION, VC, MEDIA and PROJECTION) will be LIMITED.<br> - SDL Apps which AppHMIType are same will be BACKGROUND.<br></div>|
|<div align="left"> If the SDL App HMI Level is "LIMITED", the behavior of other SDL Apps is as follows:<br><br> - All SDL Apps except SDL Media/Video Apps will keep the current status.<br> - SDL Apps which AppHMIType are different will keep the current status.<br> - SDL Apps which AppHMIType are same will be BACKGROUND.<br></div>|
|<div align="left"> If the SDL App HMI Level is "BACKGROUND", the behavior of other SDL Apps is as follows:<br><br> - All SDL Apps will keep the current status.<br></div>|

### 3.2. Status Transition Diagram
In Figure1 below shows the diagram of status transition based on the current status transition rule.
Also, Table3 below shows the various status transition and the expected behavior related to various triggers.

<div align="center">

**Figuer1.** Status Transition Diagram
![figuer1_status_transition_diagram.png](./assets/figuer1_status_transition_diagram.png)

</div>

|||
**Figuer1.** Status Transition Diagram
![figuer1_status_transition_diagram.png](./assets/figuer1_status_transition_diagram.png)
|||

**Table3.** Various Status Transition and Expected behavior related to Various Trigger
![table3_various_status_transition_and_expected_behavior_related_to_various_trigger.png](./assets/table3_various_status_transition_and_expected_behavior_related_to_various_trigger.png)

###  3.3. Competition of Video Streaming/Audio Streaming
Other than the HMI level status, SDL has also a status for VideoStreaming/AudioStreaming which can be controlled.
The following tables below show the rules of status change, when the user switches SDL App; VideoStreaming in Table4 and AudioStreaming in Table5.

**Table4.** Matrix table of Video Stream status
<table>
  <tr>
    <th colspan="2" rowspan="2">  </th>
    <th colspan="3"><div align="center"> 2nd launched </div></th>
  </tr>
  <tr>
    <th><div align="center"> NAVIGATION </div></th>
    <th><div align="center"> PROJECTION </div></th>
    <th><div align="center"> Other </div></th>
  </tr>
  <tr>
    <td rowspan="3"><div align="left"><b> 1st <br>launched </b></div></td>
    <td><div align="center"><b> NAVIGATION </b></div></td>
    <td><div align="left"> 1st&nbsp;NAVIGATION&nbsp;:&nbsp;NS<br>2nd NAVIGATION : S </div></td>
    <td><div align="left"> 1st&nbsp;NAVIGATION&nbsp;:&nbsp;NS<br>2nd PROJECTION : S </div></td>
    <td><div align="left"> 1st&nbsp;NAVIGATION&nbsp;:&nbsp;S<br>2nd Other :NS </div></td>
  </tr>
  <tr>
    <td><div align="center"><b> PROJECTION </b></div></td>
    <td><div align="left"> 1st PROJECTION : NS<br>2nd NAVIGATION : S </div></td>
    <td><div align="left"> 1st PROJECTION : NS<br>2nd PROJECTION : S </div></td>
    <td><div align="left"> 1st PROJECTION : S<br>2nd Other : NS </div></td>
  </tr>
  <tr>
    <td><div align="center"><b> Other </b></div></td>
    <td><div align="left"> 1st Other : NS<br>2nd NAVIGATION : S </div></td>
    <td><div align="left"> 1st Other : NS<br>2nd PROJECTION : S </div></td>
    <td><div align="left"> 1st Other : NS<br>2nd Other : S </div></td>
  </tr>
</table>
*S : STREAMABLE, NS : NOT_STREAMABLE<br><br>

**Table5.** Matrix table for Audio Streaming status
<table>
  <tr>
    <th colspan="2" rowspan="2">  </th>
    <th colspan="4"><div align="center"> 2nd launched </div></th>
  </tr>
  <tr>
    <th><div align="center"> NAVIGATION </div></th>
    <th><div align="center"> PROJECTION </div></th>
    <th><div align="center"> IsMediaApp </div></th>
    <th><div align="center"> Other </div></th>
  </tr>
  <tr>
    <td rowspan="4"><div align="left"><b> 1st <br>launched </b></div></td>
    <td><div align="center"><b> NAVIGATION </b></div></td>
    <td><div align="left"> 1st NAVIGATION : NA<br>2nd NAVIGATION : A </div></td>
    <td><div align="left"> 1st NAVIGATION : A<br>2nd&nbsp;PROJECTION&nbsp;:&nbsp;NA </div></td>
    <td><div align="left"> 1st NAVIGATION : A<br>2nd&nbsp;IsMediaApp&nbsp;:&nbsp;NA </div></td>
    <td><div align="left"> 1st&nbsp;NAVIGATION&nbsp;:&nbsp;A<br>2nd Other : NA </div></td>
  </tr>
  <tr>
    <td><div align="center"><b> PROJECTION </b></div></td>
    <td><div align="left"> 1st PROJECTION : A<br>2nd&nbsp;NAVIGATION&nbsp;:&nbsp;NA </div></td>
    <td><div align="left"> 1st PROJECTION : NA<br>2nd PROJECTION : A </div></td>
    <td><div align="left"> 1st PROJECTION : A<br>2nd IsMediaApp : NA </div></td>
    <td><div align="left"> 1st PROJECTION : A<br>2nd Other : NA </div></td>
  </tr>
  <tr>
    <td><div align="center"><b> IsMediaApp </b></div></td>
    <td><div align="left"> 1st IsMediaApp : A<br>2nd NAVIGATION : NA </div></td>
    <td><div align="left"> 1st IsMediaApp : A<br>2nd PROJECTION : NA </div></td>
    <td><div align="left"> 1st IsMediaApp : NA<br>2nd IsMediaApp : A </div></td>
    <td><div align="left"> 1st IsMediaApp : A<br>2nd Other : NA </div></td>
  </tr>
  <tr>
    <td><div align="center"><b> Other </b></div></td>
    <td><div align="left"> 1st Other : NA<br>2nd NAVIGATION : A </div></td>
    <td><div align="left"> 1st Other : NA<br>2nd PROJECTION : A </div></td>
    <td><div align="left"> 1st Other : NA<br>2nd IsMediaApp : A </div></td>
    <td><div align="left"> 1st Other : NA<br>2nd Other : A </div></td>
  </tr>
</table>
*A : AUDIBLE, NA : NOT_AUDIBLE