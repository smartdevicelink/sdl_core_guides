# 2.2. Launch by Voice Recognition

## 1. Overview
This chapter describes the launch of the SDL App by Voice Recognition (pushing Push To Talk (PTT) button).

## 2. Background/Purpose/Reason for Standardization
Currently, the launch of the SDL App is an SDL standard behavior.
However, since triggers that launch the SDL App are not explicitly defined in the SDL standard specification, it is necessary for the OEMs to define it by themselves.
Hence, the purpose of this document is to standardize such cases/issues using the TOYOTA specification, in order to be able to contribute to the SDL Ecosystem.

## 3. Function Details
### 3.1. Function Overview
The user operations that trigger the launch of SDL App are listed below:

<ol>
 (1) By pressing the SDL App Icon<br>
 (2) By launching Voice Recognition<br>
 (3) By choosing the Audio source on the Audio screen<br>
 (4) By pressing the Hard SW<br>
 (5) By pressing the ModeSW on the Steering Wheel
</ol>

The following (1), (3), (4), (5) mentioned above, already each has of their own documents. Thus, this chapter provides information about (2).

### 3.2. Launch the SDL App by Voice recognition
By short press of the PTT button, voice recognition is activated, and the user can launch and display the SDL App on HU by recognizing the SDL App name.
Even after launching the SDL App, you can still continue to use Voice Recognition.
The Voice Recognition is canceled, when it detects a voice key word or a silence during the voice recognition state.
In addition, if the HU receives a launching request of the SDL App by Voice Recognition, then the HU will decide that the SDL App can not be launched, (in this case, when the mobile is not connected or the SDL app is not registed, etc.)
Then the HU should notify an error to user.

The SDL App can be launched by Voice Recognition with Wakeup Word without pressing down the PTT button.
After detecting the Wakeup Word, the user can continuously use the same microphone for Voice Recognition to launch the SDL App.
The Wakeup Word is specified in OEM's specification.


### 3.3. Pronounciation data of App name (ttsName)
The ttsName is a pronounciation data of SDL App name and it is used for specifying the SDL App.
The ttsName has multiple pronounciation data as candidates to identify SDL App name, and the format is in text.
The maximum number of pronounciations data that can be registered for one SDL App is up to 100.
And, the maximum length of pronounciation data is 500 characters.
SDL App name, pronounciation data, and other App information are obtained from SDL App when the SDL App is registed by RPC"RegisterAppInterface", then the Voice Recognition command is registered.


#### 3.3.1. Registration of ttsName
The timing for registering the acquired App name phoneme data (ttsName), App name (appName), and alias definition for speech recognition (vrSynonyms) to the speech recognition function is:
<ol>
 (1) After 60 seconds have passed since device connection or language switching, all cached SDL Apps <ol>will be registered at once.</ol>
 (2) Following (1), every time there is a new registration from the SDL App, all cached SDL Apps will be registered <ol>at once.</ol>
</ol>

The registration sequence of ttsName for the voice recognition function is as follows.

<div align="center">

![Figure1_The_registration_sequence_of_ttsName.png](./assets/Figure1_The_registration_sequence_of_ttsName.png)<br>
<b>Figure1.</b> The registration sequence of ttsName

</div>

#### 3.3.2. Deletion of ttsName
When ttsName, appName, and vrSynonyms are updated, the voice recognition commands for launching the SDL App are also updated, and old commands are deleted.
Also, when the SDL feature is disabled (for example, disabled by OTA), the speech recognition commands in the SDL App are removed.

## 4. Differences from the SDL standard specification
The trigger that launches the SDL App is not explicitly defined in the SDL standard specification, because it is processed in the HMI.
(Information such as the maximum number of registration of ttsName, and characters for one device are the only ones specified.)
Therefore, all of the contents describe in "3. Function Details" differ from the existing SDL Standard Specification.

## 5. Sequence Diagrams
[Prerequisites]<br>
The SDL App must be registered.

1. The user presses the PTT button to activate voice recognition.
2. The user calls the SDL App name.
3. Then, the HMI will start the SDL App launch sequence.
<br>*Refer to "1.1. Establish_Session" for the SDL App launch sequence.

<div align="center">

![Figure2_The_SDLApp_launch_is_ok.png](./assets/Figure2_The_SDLApp_launch_is_ok.png)<br>
<b>Figure2.</b> The SDL App launch by voice recognition.(In case the launch is possible)

</div>

If the SDL App has not been registered, the operation shown in the sequence diagram below will be executed.

<div align="center">

![Figure3_The_SDLApp_launch_is_ng.png](./assets/Figure3_The_SDLApp_launch_is_ng.png)<br>
<b>Figure3.</b> The SDL App launch by voice recognition. (In case the launch is not possible)

</div>

## 6. Impacted Platforms
Changes impact the following platform/s:
- HMI