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

<table>
  <tr>
    <th align="center" rowspan="2"> ID </th>
    <th align="center" rowspan="2"> Notify/Request </th>
    <th align="center" rowspan="2"> EVENTID </th>
    <th align="center" rowspan="2"> Parameter </th>
    <th align="center" rowspan="2"> APPHMITYPE </th>
    <th align="center" colspan="3"> BEFORE Change </th>
    <th align="center" colspan="3"> AFTER Change </th>
    <th align="center" rowspan="2"> App Behavior </th>
  </tr>
  <tr>
    <th align="center"> HMI_STATE </th>
    <th align="center"> AUDIO_STATE </th>
    <th align="center"> VIDEO_STATE </th>
    <th align="center"> HMI_STATE </th>
    <th align="center"> AUDIO_STATE </th>
    <th align="center"> VIDEO_STATE </th>
  </tr>
  <tr>
    <td align="center"> 1 </td>
    <td align="center"> OnEventChanged() </td>
    <td align="center"> PHONE_CALL </td>
    <td align="center"> TRUE </td>
    <td align="center"> ALL </td>
    <td align="center"> FULL/LIMITED </td>
    <td align="center"> AUDIBLE </td>
    <td align="center"> STREAMABLE/<br>NOT_STREAMABLE </td>
    <td align="center"> BACKGROUND </td>
    <td align="center"> NOT_AUDIBLE </td>
    <td align="center"> Keep&nbsp;previous&nbsp;status </td>
    <td align="center"> Audio state is stopped </td>
  </tr>
  <tr>
    <td align="center"> 2 </td>
    <td align="center"> OnEventChanged() </td>
    <td align="center"> EMERGENCY_<br>EVENT </td>
    <td align="center"> TRUE </td>
    <td align="center"> ALL </td>
    <td align="center"> FULL/LIMITED </td>
    <td align="center"> AUDIBLE </td>
    <td align="center"> STREAMABLE/<br>NOT_STREAMABLE </td>
    <td align="center"> BACKGROUND </td>
    <td align="center"> NOT_AUDIBLE </td>
    <td align="center"> Keep previous status </td>
    <td align="center"> Audio state is stopped </td>
  </tr>
  <tr>
    <td align="center"> 3 </td>
    <td align="center"> OnEventChanged() </td>
    <td align="center"> DEACTIVATE_HMI </td>
    <td align="center"> TRUE </td>
    <td align="center"> ALL </td>
    <td align="center"> FULL/LIMITED </td>
    <td align="center"> AUDIBLE </td>
    <td align="center"> STREAMABLE/<br>NOT_STREAMABLE </td>
    <td align="center"> BACKGROUND </td>
    <td align="center"> NOT_AUDIBLE </td>
    <td align="center"> Keep previous status </td>
    <td align="center"> Audio state is stopped </td>
  </tr>
  <tr>
    <td align="center"> 4 </td>
    <td align="center"> OnEventChanged() </td>
    <td align="center"> AUDIO_SOURCE </td>
    <td align="center"> TRUE </td>
    <td align="center"> Media </td>
    <td align="center"> FULL/LIMITED </td>
    <td align="center"> AUDIBLE </td>
    <td align="center"> STREAMABLE/<br>NOT_STREAMABLE </td>
    <td align="center"> BACKGROUND </td>
    <td align="center"> NOT_AUDIBLE </td>
    <td align="center"> Keep previous status </td>
    <td align="center"> Audio state is stopped </td>
  </tr>
  <tr>
    <td align="center"> 5 </td>
    <td align="center"> OnEventChanged() </td>
    <td align="center"> AUDIO_SOURCE </td>
    <td align="center"> TRUE </td>
    <td align="center"> Navigation </td>
    <td align="center"> FULL/LIMITED </td>
    <td align="center"> AUDIBLE </td>
    <td align="center"> STREAMABLE/<br>NOT_STREAMABLE </td>
    <td align="center"> LIMITED </td>
    <td align="center"> AUDIBLE </td>
    <td align="center"> Keep previous status </td>
    <td align="center"> Guidance voice is not impacted </td>
  </tr>
  <tr>
    <td align="center"> 6 </td>
    <td align="center"> OnEventChanged() </td>
    <td align="center"> AUDIO_SOURCE </td>
    <td align="center"> TRUE </td>
    <td align="center"> Non-media </td>
    <td align="center"> FULL/LIMITED </td>
    <td align="center"> AUDIBLE </td>
    <td align="center"> STREAMABLE/<br>NOT_STREAMABLE </td>
    <td align="center"> BACKGROUND </td>
    <td align="center"> NOT_AUDIBLE </td>
    <td align="center"> Keep previous status </td>
    <td align="center"> Audio state is stopped </td>
  </tr>
  <tr>
    <td align="center"> 7 </td>
    <td align="center"> OnEventChanged() </td>
    <td align="center"> EMBEDDED_NAVI </td>
    <td align="center"> TRUE </td>
    <td align="center"> Media </td>
    <td align="center"> FULL/LIMITED </td>
    <td align="center"> AUDIBLE </td>
    <td align="center"> STREAMABLE/<br>NOT_STREAMABLE </td>
    <td align="center"> LIMITED </td>
    <td align="center"> AUDIBLE </td>
    <td align="center"> Keep previous status </td>
    <td align="center"> Media Audio is not impacted </td>
  </tr>
  <tr>
    <td align="center"> 8 </td>
    <td align="center"> OnEventChanged() </td>
    <td align="center"> EMBEDDED_NAVI </td>
    <td align="center"> TRUE </td>
    <td align="center"> Navigation </td>
    <td align="center"> FULL/LIMITED </td>
    <td align="center"> AUDIBLE </td>
    <td align="center"> STREAMABLE/<br>NOT_STREAMABLE </td>
    <td align="center"> BACKGROUND </td>
    <td align="center"> NOT_AUDIBLE </td>
    <td align="center"> Keep previous status </td>
    <td align="center"> Audio state is stopped </td>
  </tr>
  <tr>
    <td align="center"> 9 </td>
    <td align="center"> OnEventChanged() </td>
    <td align="center"> EMBEDDED_NAVI </td>
    <td align="center"> TRUE </td>
    <td align="center"> Non-media </td>
    <td align="center"> FULL/LIMITED </td>
    <td align="center"> AUDIBLE </td>
    <td align="center"> STREAMABLE/<br>NOT_STREAMABLE </td>
    <td align="center"> BACKGROUND </td>
    <td align="center"> NOT_AUDIBLE </td>
    <td align="center"> Keep previous status </td>
    <td align="center"> Audio state is stopped </td>
  </tr>
  <tr>
    <td align="center"> 10 </td>
    <td align="center"> OnExitApplication() </td>
    <td align="center"> No Parameter </td>
    <td align="center"> reason = DRIVER_DISTRACTION_VIOLATION </td>
    <td align="center"> ALL </td>
    <td align="center"> FULL/LIMITED/<br>BACKGROUND </td>
    <td align="center"> AUDIBLE/<br>NOT_AUDIBLE </td>
    <td align="center"> STREAMABLE/<br>NOT_STREAMABLE </td>
    <td align="center"> NONE </td>
    <td align="center"> NOT_AUDIBLE </td>
    <td align="center"> NOT_STREAMABLE </td>
    <td align="center"> Audio voice and Video streaming are stopped </td>
  </tr>
  <tr>
    <td align="center"> 11 </td>
    <td align="center"> OnExitApplication() </td>
    <td align="center"> No Parameter </td>
    <td align="center"> reason = USER_EXIT </td>
    <td align="center"> ALL </td>
    <td align="center"> FULL/LIMITED/<br>BACKGROUND </td>
    <td align="center"> AUDIBLE/<br>NOT_AUDIBLE </td>
    <td align="center"> STREAMABLE/<br>NOT_STREAMABLE </td>
    <td align="center"> NONE </td>
    <td align="center"> NOT_AUDIBLE </td>
    <td align="center"> NOT_STREAMABLE </td>
    <td align="center"> Audio voice and Video streaming are stopped </td>
  </tr>
  <tr>
    <td align="center"> 12 </td>
    <td align="center"> OnExitApplication() </td>
    <td align="center"> No Parameter </td>
    <td align="center"> reason = CLOSE_CLOUD_CONNECTION </td>
    <td align="center"> ALL </td>
    <td align="center"> FULL/LIMITED/<br>BACKGROUND </td>
    <td align="center"> AUDIBLE/<br>NOT_AUDIBLE </td>
    <td align="center"> STREAMABLE/<br>NOT_STREAMABLE </td>
    <td align="center"> NONE </td>
    <td align="center"> NOT_AUDIBLE </td>
    <td align="center"> NOT_STREAMABLE </td>
    <td align="center"> Audio voice and Video streaming are stopped </td>
  </tr>
  <tr>
    <td align="center"> 13 </td>
    <td align="center"> SDL.ActivateApp </td>
    <td align="center"> No Parameter </td>
    <td align="center"> No Parameter </td>
    <td align="center"> ALL </td>
    <td align="center"> FULL/LIMITED/<br>BACKGROUND </td>
    <td align="center"> AUDIBLE/<br>NOT_AUDIBLE </td>
    <td align="center"> STREAMABLE/<br>NOT_STREAMABLE </td>
    <td align="center"> FULL </td>
    <td align="center" colspan="2"> Audio and Video Status<br>(Set by specific App type) </td>
    <td align="left"> Audio&nbsp;voice&nbsp;and&nbsp;Video&nbsp;streaming&nbsp;are&nbsp;depended&nbsp;on&nbsp;AppHMIType&nbsp;parameter<br> - Media App status : AUDIBLE, NOT_STREAMABLE<br> - NAVIGATION App status : AUDIBLE, STREAMABLE </td>
  </tr>
  <tr>
    <td align="center"> 14 </td>
    <td align="center"> RegisterAppInterface </td>
    <td align="center"> No Parameter </td>
    <td align="center"> No Parameter </td>
    <td align="center"> ALL </td>
    <td align="center"> No status<br>before&nbsp;Register </td>
    <td align="center"> No status<br>before&nbsp;Register </td>
    <td align="center"> No status<br>before&nbsp;Register </td>
    <td align="center"> NONE </td>
    <td align="center"> NOT_AUDIBLE </td>
    <td align="center"> NOT_STREAMABLE </td>
    <td align="center"> Audio voice and Video streaming are stopped </td>
  </tr>
  <tr>
    <td align="center"> 15 </td>
    <td align="center"> OnAppDeactivated </td>
    <td align="center"> No Parameter </td>
    <td align="center"> No Parameter </td>
    <td align="center"> Navigation </td>
    <td align="center"> FULL </td>
    <td align="center"> AUDIBLE </td>
    <td align="center"> STREAMABLE </td>
    <td align="center"> LIMITED </td>
    <td align="center"> AUDIBLE </td>
    <td align="center"> STREAMABLE </td>
    <td align="center"> Audio voice and Video streaming are not impacted </td>
  </tr>
  <tr>
    <td align="center"> 16 </td>
    <td align="center"> OnAppDeactivated </td>
    <td align="center"> No Parameter </td>
    <td align="center"> No Parameter </td>
    <td align="center"> PROJECTION &<br>isMedia = false </td>
    <td align="center"> FULL </td>
    <td align="center"> NOT_AUDIBLE </td>
    <td align="center"> STREAMABLE </td>
    <td align="center"> LIMITED </td>
    <td align="center"> NOT_AUDIBLE </td>
    <td align="center"> STREAMABLE </td>
    <td align="center"> Audio voice is stopped, however Video streaming is not impacted </td>
  </tr>
  <tr>
    <td align="center"> 17 </td>
    <td align="center"> OnAppDeactivated </td>
    <td align="center"> No Parameter </td>
    <td align="center"> No Parameter </td>
    <td align="center"> Non-media &<br>Non-navi </td>
    <td align="center"> FULL/LIMITED/<br>BACKGROUND </td>
    <td align="center"> AUDIBLE/<br>NOT_AUDIBLE </td>
    <td align="center"> STREAMABLE/<br>NOT_STREAMABLE </td>
    <td align="center"> BACKGROUND </td>
    <td align="center"> NOT_AUDIBLE </td>
    <td align="center"> NOT_STREAMABLE </td>
    <td align="center"> Audio voice and Video streaming are stopped </td>
  </tr>
  <tr>
    <td align="center"> 18 </td>
    <td align="center"> SDL.ActivateApp(app1)<br>->SDL.ActivateApp(app2) </td>
    <td align="center"> No Parameter </td>
    <td align="center"> No Parameter </td>
    <td align="center"> App1&nbsp;and&nbsp;App2&nbsp;are<br>same type </td>
    <td align="center"> App1_FULL </td>
    <td align="center"> AUDIBLE </td>
    <td align="center"> STREAMABLE </td>
    <td align="center"> App1_BACKGROUND </td>
    <td align="center"> NOT_AUDIBLE </td>
    <td align="center"> NOT_STREAMABLE </td>
    <td align="center"> Audio voice and Video streaming are stopped </td>
  </tr>
  <tr>
    <td align="center"> 19 </td>
    <td align="center"> SDL.ActivateApp(app1)<br>->SDL.ActivateApp(app2) </td>
    <td align="center"> No Parameter </td>
    <td align="center"> No Parameter </td>
    <td align="center"> App1 and App2 are<br>different type </td>
    <td align="center"> App1_FULL </td>
    <td align="center"> AUDIBLE </td>
    <td align="center"> STREAMABLE </td>
    <td align="center"> App1_LIMITED </td>
    <td align="center" colspan="2"> Audio and Video Status<br>(Set by specific App type) </td>
    <td align="left"> Audio voice and Video streaming are depended on AppHMIType parameter<br> - Media App status : AUDIBLE, NOT_STREAMABLE<br> - NAVIGATION App status : AUDIBLE, STREAMABLE </td>
  </tr>
  <tr>
    <td align="center"> 20 </td>
    <td align="left"> SDL.ActivateApp(VideoApp1)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br>->SDL.ActivateApp(MediaApp2)<br>->SDL.ActivateApp(VideoApp3) </td>
    <td align="center"> No Parameter </td>
    <td align="center"> No Parameter </td>
    <td align="center"> VideoApp1,<br>MeidaApp2,<br>VideoApp3 </td>
    <td align="center"> VideoApp1_LIMITED </td>
    <td align="center"> VideoApp1_<br>AUDIBLE </td>
    <td align="center"> VideoApp1_<br>STREAMABLE </td>
    <td align="center"> VideoApp1_BACKGROUND </td>
    <td align="center"> VideoApp1_<br>NOT_AUDIBLE </td>
    <td align="center"> VideoApp1_<br>NOT_STREAMABLE </td>
    <td align="center"> Audio voice and Video streaming are stopped </td>
  </tr>
  <tr>
    <td align="center"> 21 </td>
    <td align="left"> SDL.ActivateApp(MediaApp1)<br>->SDL.ActivateApp(VideoApp2)<br>->SDL.ActivateApp(MediaApp3) </td>
    <td align="center"> No Parameter </td>
    <td align="center"> No Parameter </td>
    <td align="center"> MediaApp1,<br>VideoApp2,<br>MediaApp3 </td>
    <td align="center"> MediaApp1_LIMITED </td>
    <td align="center"> MediaApp1_<br>AUDIBLE </td>
    <td align="center"> MediaApp1_<br>NOT_STREAMABLE </td>
    <td align="center"> MediaApp1_BACKGROUND </td>
    <td align="center"> MediaApp1_<br>NOT_AUDIBLE </td>
    <td align="center"> MediaApp1_<br>NOT_STREAMABLE </td>
    <td align="center"> Audio voice and Video streaming are stopped </td>
  </tr>
</table>

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
    <td align="left"> 1st&nbsp;NAVIGATION&nbsp;:&nbsp;NS<br>2nd NAVIGATION : S </td>
    <td align="left"> 1st&nbsp;NAVIGATION&nbsp;:&nbsp;NS<br>2nd PROJECTION : S </td>
    <td align="left"> 1st&nbsp;NAVIGATION&nbsp;:&nbsp;S<br>2nd Other :NS </td>
  </tr>
  <tr>
    <td align="center"><b> PROJECTION </b></td>
    <td align="left"> 1st PROJECTION : NS<br>2nd NAVIGATION : S </td>
    <td align="left"> 1st PROJECTION : NS<br>2nd PROJECTION : S </td>
    <td align="left"> 1st PROJECTION : S<br>2nd Other : NS </td>
  </tr>
  <tr>
    <td align="center"><b> Other </b></td>
    <td align="left"> 1st Other : NS<br>2nd NAVIGATION : S </td>
    <td align="left"> 1st Other : NS<br>2nd PROJECTION : S </td>
    <td align="left"> 1st Other : NS<br>2nd Other : S </td>
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
    <td align="left"> 1st NAVIGATION : NA<br>2nd NAVIGATION : A </td>
    <td align="left"> 1st NAVIGATION : A<br>2nd&nbsp;PROJECTION&nbsp;:&nbsp;NA </td>
    <td align="left"> 1st NAVIGATION : A<br>2nd&nbsp;IsMediaApp&nbsp;:&nbsp;NA </td>
    <td align="left"> 1st&nbsp;NAVIGATION&nbsp;:&nbsp;A<br>2nd Other : NA </td>
  </tr>
  <tr>
    <td align="center"><b> PROJECTION </b></td>
    <td align="left"> 1st PROJECTION : A<br>2nd&nbsp;NAVIGATION&nbsp;:&nbsp;NA </td>
    <td align="left"> 1st PROJECTION : NA<br>2nd PROJECTION : A </td>
    <td align="left"> 1st PROJECTION : A<br>2nd IsMediaApp : NA </td>
    <td align="left"> 1st PROJECTION : A<br>2nd Other : NA </td>
  </tr>
  <tr>
    <td align="center"><b> IsMediaApp </b></td>
    <td align="left"> 1st IsMediaApp : A<br>2nd NAVIGATION : NA </td>
    <td align="left"> 1st IsMediaApp : A<br>2nd PROJECTION : NA </td>
    <td align="left"> 1st IsMediaApp : NA<br>2nd IsMediaApp : A </td>
    <td align="left"> 1st IsMediaApp : A<br>2nd Other : NA </td>
  </tr>
  <tr>
    <td align="center"><b> Other </b></td>
    <td align="left"> 1st Other : NA<br>2nd NAVIGATION : A </td>
    <td align="left"> 1st Other : NA<br>2nd PROJECTION : A </td>
    <td align="left"> 1st Other : NA<br>2nd IsMediaApp : A </td>
    <td align="left"> 1st Other : NA<br>2nd Other : A </td>
  </tr>
</table>
* A : AUDIBLE, NA : NOT_AUDIBLE