# 2.3 Launch by choosing the Audio source on the Audio screen

## 1. Overview
This chapter describes the launch of the SDL App from the Audio source selection screen. The Icon of SDL Media App which AppHMIType is "MEDIA" will be displayed on the audio source selection screen.

## 2. Background/Purpose/Reason for Standardization
Currently, the launch of the SDL App is an SDL standard behavior. However, since triggers that launch SDL App are not explicitly defined in the SDL standard specification, it is necessary for the OEMs to define it by themselves. Hence, the purpose of this document is to standardize such cases/issues using the TOYOTA specification, in order to be able to contribute to the SDL Ecosystem.

## 3. Function Details
### 3.1. Function Overview
The user operations that trigger the launch of SDL App are listed below:

(1) By pressing the SDL App Icon
(2) By launching Voice Recognition
(3) By choosing the Audio source on the Audio screen
(4) By pressing the Hardware button
 (5) By pressing the ModeSwitch(ModeSW*) on the Steering Wheel

!!! NOTE
*ModeSW is the button set in the Steering Wheel to switch the Audio Source.
!!!

The following (1), (2), (4), (5) mentioned above, already each has of their own documents. Thus, this chapter provides information about (3).

### 3.2. Press App icon on the Audio source selection screen.
The SDL App can be launched by pressing the SDL App Icon displayed on the Audio Source selection screen. If the Native Audio is running, it will stop. If other SDL Media App is running, that App will be in "BACKGROUND" state.

## 4. Differences from the SDL standard specification
The trigger that launches the SDL App is not explicitly defined in the SDL standard specification, because it is processed in the HMI. Therefore, all of the contents describe in "3. Function Details" differ from the existing SDL standard specification.

## 5. Sequence Diagrams
1. The user presses the Audio button.
2. The Audio source selection screen is displayed.
3. The user presses the SDL App icon on the Audio source selection screen.
4. Then, the HMI will start the SDL App launch sequence.
\*1 The display of the Audio source selection screen during 1 and 2, is specified by the OEMs.
\*2 Refer to “1.1. Establish Session” for the SDL App launch sequence.

|||
**Figure1.** The launch of SDL App by choosing the Audio source on the Audio screen
![Figure1_launch_of_SDLApp_by_choosing_the_AudioSource.png](./assets/Figure1_launch_of_SDLApp_by_choosing_the_AudioSource.png)
|||

## 5. Impacted Platforms
Changes impact the following platform/s:

- HMI
