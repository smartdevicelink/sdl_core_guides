# 2.1. Press the App Icon on the SDL menu screen

## 1. Overview
This chapter describes the launch of the SDL App by pressing the SDL App Icon on the main menu.

## 2. Background/Purpose/Reason for Standardization
Currently, the launch of the SDL App is an SDL standard behavior. However, since triggers that launch SDL App are not explicitly defined in the SDL standard specification, it is necessary for the OEMs to define it by themselves. Hence, the purpose of this document is to standardize such cases/issues using the TOYOTA specification, in order to be able to contribute to the SDL Ecosystem.

## 3. Function Details
### 3.1. Function Overview
The user operations that trigger the launch of SDL App are listed below:

(1) By pressing the SDL App Icon
(2) By launching Voice Recognition
(3) By choosing the Audio source on the Audio screen
(4) By pressing the Hardware button
(5) By pressing the ModeSW on the Steering Wheel

The following (2)-(5) mentioned above, already each has of their own documents. Thus, this chapter provides information about (1).


## 4. Differences from the SDL standard specification
The launch of SDL App by pressing the SDL App Icon is not explicitly defined in the SDL standard specification, because it is processed in the HMI. Therefore, all of the contents describe in "5. Sequence Diagrams" differ from the existing SDL standard specification.

## 5. Sequence Diagrams

1. The user presses the SDL App Icon.
2. Then, the HMI will start the SDL App launch sequence.
 *Refer to "1.1. Establish Session" for the SDL App launch sequence.

|||
**Figure1.** The launch of SDL App by pressing the SDL App Icon
![Figure1_The_launch_of_SDLApp_by_pressing_the_SDLAppicon.png](./assets/Figure1_The_launch_of_SDLApp_by_pressing_the_SDLAppicon.png)
|||

## 6. Impacted Platforms
Changes impact the following platform/s:

- HMI



