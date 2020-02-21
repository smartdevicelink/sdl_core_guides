# 1.1. Establish Session

## 1. Overview
This chapter describes the launch sequence of normal type, MEDIA type and the NAVIGATION/PROJECTION type apps.

The application type (AppType) is determined by AppHMIType.
Details are shown in Table1.

**Table1.** AppType categorized by each AppHMIType
<table>
  <tr>
    <th align="center"> App Type </th>
    <th align="center"> AppHMIType </th>
  </tr>
  <tr>
     <td align="left" rowspan="9"> Normal type App </td>
     <td align="left"> DEFAULT </td>
  </tr>
  <tr>
     <td align="left"> COMMUNICATION </td>
  </tr>
  <tr>
     <td align="left"> MESSAGING </td>
  </tr>
  <tr>
     <td align="left"> INFORMATION </td>
  </tr>
  <tr>
     <td align="left"> SOCIAL </td>
  </tr>
  <tr>
     <td align="left"> BACKGROUND_PROCESS </td>
  </tr>
  <tr>
     <td align="left"> TESTING </td>
  </tr>
  <tr>
     <td align="left"> SYSTEM </td>
  </tr>
  <tr>
     <td align="left"> REMOTE_CONTROL </td>
  </tr>
  <tr>
     <td align="left"> MEDIA type App </td>
     <td align="left"> MEDIA </td>
  </tr>
  <tr>
     <td align="left" rowspan = "2"> NAVIGATION / PROJECTION type App </td>
     <td align="left"> NAVIGATION </td>
  </tr>
  <tr>
     <td align="left"> PROJECTION </td>
  </tr>
</table>

## 2. Background/Purpose/Reason for Standardization
Currently, the launch of SDL App is an SDL standard behavior.
However, the SDL Standard Specification lacks definition for the following points:
<ol>
  (1) SDL App launching sequence of App side (especially RPC communication sequence) is not defined.<br>
  (2) The launch sequence for normal type SDL App is defined, while the launch sequence for MEDIA Type and <ol>NAVIGATION/PROJECTION Type are not.</ol>
</ol>
Therefore, a misunderstanding might occur between App developers and HMI developers.
The purpose of this document is to clarify the launch sequence of SDL Apps in order to be able to contribute to the SDL Ecosystem.

## 3. Function Details
### 3.1. Function Overview
The following startup sequences are to be clarified.
<ol>
  (1) Normal type App<br>
  (2) MEDIA type App<br>
  (3) NAVIGATION / PROJECTION type App<br>
</ol>


### 3.2 SDL App launch sequence in the SDL standard specification
The SDL App launch sequence in the SDL Standard Specification is below Figure1.

<div align="center">

![Figure1_Sequence_of_SDL_App_launch.png](./assets/Figure1_Sequence_of_SDL_App_launch.png)<br>
<b>Figure1.</b> Sequence of SDL App launch provided in the SDL Standard specification

</div>

To launch SDL App, additional communication is needed in the red frame of Figure1. However, currently that additional communication is not discribed.
For details about "Connect to Device" , please refer to the "Establishing a Transport" chapter of SDL Overview Guides on the SDLC official website.
And since there is no lack of information (on the topics mentioned) , these topic will be omitted in this chapter.

Furthermore, the SDL website also provides information on how to establish a connection using AOA, BT and iAP. 
And since there is no lack of information (on the topics mentioned) , these topic will be omitted in this chapter.


### 3.3. Changes in the launch sequence of normal type App
The following are additional behaviors required in the red frame of Figure1 in case of the normal type app will be launched.

  When the SDL App receives RPC"OnHMIStatus" with HMI Level FULL, the HMI displays the SDL App on foreground of the HU screen.
  After the App is displayed on the foreground, the SDL App sets the HMI template by sending RPC"SetDisplaylayout". 


### 3.4. Changes in the launch sequence of MEDIA type App
The following are additional behaviors required in the red frame of Figure1 in case of the MEDIA type app will be launched.

 When the SDL App receives RPC"OnHMIStatus" with HMI Level FULL, the HMI displays the SDL App on foreground of the HU screen.
  After the app is displayed on the foreground, the SDL App sets the HMI template by sending RPC"SetDisplaylayout".
  Then, after the SDL App receives RPC"showResponse", it sets a progress bar on the HU screen by sending RPC"SetMediaClockTimer".
  The SDL App sends RPC"SubscribeButton" and monitors the HMI Button event.

### 3.5. Changes in the launch sequence of NAVIGATION/PROJECTION type App
The following additional/excluded behaviores are required in the red frame of Figure1 in case the NAVIGATION/PROJECTION type app is launched.

  When the SDL App receives RPC"OnHMIStatus" with HMI Level FULL, the HMI displays the SDL App on foreground of the HU screen.
  After the App is displayed on the foreground, the SDL App does NOT send RPC"show".

 [The following applies only to the Android version of SDL App]<br>
  After the app is displayed to the foreground, (the SDL App) starts VideoStreamingManager/AudioStreamingManager.
  Then, when VideoStreamingManager/AudioStreamingManager has started successfully, it sends the video/audio data to the HU.


## 4. Differences from the SDL standard specification
The red frame in Figure1, which is supposed to be an essential part of the sequence, is not explicitly defined in the SDL standard specification.
Therefore, all of the contents describe in 3.3 - 3.4 differ from the existing SDL standard specification.


## 5. Sequence Diagrams
The three types of launch sequence are described based on the SDL App launch sequence (Figure1) published in the SDL Standard Specification.
<ol>
  (1) Normal type App launch sequence (Figure2)<br>
  (2) MEDIA type App launch sequence (Figure3)<br>
  (3) NAVIGATION / PROJECTION type App launch sequence (Figure4)<br>
</ol>
Additional sequence is added in the red frame of Figure1 for each App type.
Parts other than the red frame are common to/shared by all App type, and there are no lack in the sequences.
The sequence described in the red part refers to the RPC communication sequence; it is not described in the SDL standard specification.
<br>
<br>

<div align="center">

![Figure2_Normal_type_App_launch_sequence.png](./assets/Figure2_Normal_type_App_launch_sequence.png)<br>
<b>Figure2.</b> Normal type App launch sequence
<br>
<br>
<br>

![Figure3_MEDIA_type_App_launch_sequence.png](./assets/Figure3_MEDIA_type_App_launch_sequence.png)<br>
<b>Figure3.</b> MEDIA type App launch sequence
<br>
<br>
<br>

![Figure4_NAVIGATION-PROJECTION_type_App_launch_sequence.png](./assets/Figure4_NAVIGATION-PROJECTION_type_App_launch_sequence.png)<br>
<b>Figure4.</b> NAVIGATION / PROJECTION type App launch sequence

</div>

## 6. Impacted Platforms
Changes only affects the SDLC Guideline documents.
There is no impact to the SDL Core and Proxy.

