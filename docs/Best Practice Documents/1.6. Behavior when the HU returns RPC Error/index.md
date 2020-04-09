# 1.6. Behavior when the HU returns RPC Error

## 1. Overview
This chapter describes the behavior when the SDL App terminates unexpectedly.

## 2. Background/Purpose/Reason for Standardization
Currently, the process of SDL Apps during an unexpected termination is an SDL standard behavior. However, the process itself is not explicitly defined in the SDL standard specifications. Hence, the purpose of this document is to standardize such cases/issues using the TOYOTA specification, in order to be able to contribute to the SDL Ecosystem.

## 3. Function Details
### 3.1 For iOS
If the SDL App that is running on mobile is terminated unexpectedly, the App Connection will be disconnected. Then, the SDLCore on the HU detects that the connection has been disconnected, and sends `OnAppUnregistered` to the HMI. The HMI receives the notification and displays an error message.

|||
**Figure1.** When a running SDL App on mobile terminates unexpectedly
![Figure1_sequence_of_SDLApp_for_iOS_terminates_unexpectedly.png](./assets/Figure1_sequence_of_SDLApp_for_iOS_terminates_unexpectedly.png)
|||

### 3.2 For Android
(1) If the SDL App that running on mobile has been terminated unexpectedly, but the RouterService has not, RouterService detects the interruption of SDL App session. And SDLCore receives `UnregisterAppInterface`, and sends `OnAppUnregistered` to the HMI. The HMI receives the notification and displays an error message.
|||
**Figure2.** When a running SDL App on mobile terminates unexpectedly and the RouterService is running.
![Figure2_sequence_of_SDLApp_for_Android_terminates_unexpectedly_01.png](./assets/Figure2_sequence_of_SDLApp_for_Android_terminates_unexpectedly_01.png)
|||

(2) If the RouterService on mobile has been terminated unexpectedly, the SDLCore detects the disconnection and sends `OnAppUnregistered` to the HMI. The HMI receives the notification and displays an error message.

|||
**Figure3.** When the RouterService on mobile terminates unexpectedly
![Figure3_sequence_of_SDLApp_for_Android_terminates_unexpectedly_02.png](./assets/Figure3_sequence_of_SDLApp_for_Android_terminates_unexpectedly_02.png)
|||

## 4. Impacted Platforms
There is no impact on any Platforms.
