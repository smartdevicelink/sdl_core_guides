# Index of Best Practice documents

### [1.1. Establish Session](./1.1.Establish_Session/index.md)
<ol>
This chapter describes the launch sequence of normal type, MEDIA type and the NAVIGATION/PROJECTION type apps.
</ol>

### [1.2. SDL App Icon Display](./1.2.SDL_App_Icon_Display/index.md)
<ol>
This chapter describes how the HU displays the SDL App Icon on the screen by the connecting Mobile to the HU.
</ol>

### [1.3. SDL App Icon Caching](./1.3.SDL_App_Icon_Caching/index.md)
<ol>
This chapter describes the caching of SDL App Icon on the SDL menu screen.
</ol>

### [1.4. Capabilities Definition with TOYOTA Settings](./1.4.Capabilities_Definition_with_TOYOTA_Settings/index.md)
<ol>
This chapter describes about the capability, a function setting information that is notified from the HMI to the SDL Core.
The Specification of Capability is defined by the OEMs, however, as a reference information, this chapter discribes Capability setting as TOYOTA specification.
</ol>

### [1.5. Definitions when the HU returns RPC Error](./1.5.Definitions_when_the_HU_returns_RPC_Error/index.md)
<ol>
This chapter describes the RPC in MOBILE_API.xml and HMI_API.xml that has a lack of or incorrect description.
In addition, we have modified the incorrect definition and complemented the missing details on these RPC parameters.
</ol>

### [1.6. Behavior when the HU returns RPC Error](./1.6.Behavior_when_the_HU_returns_RPC_Error/index.md)
<ol>
This chapter describes the behavior when the SDL App terminates unexpectedly.
</ol>

### [2.1. Press the App icon on the SDL menu screen](./2.1.Press_the_App_Icon_on_the_SDL_menu_screen/index.md)
<ol>
This chapter describes the launch of the SDL App by pressing the SDL App icon on the main menu.
</ol>

### [2.2. Launch by Voice Recognition](./2.2.Launch_by_Voice_Recognition/index.md)
<ol>
This chapter describes the launch of the SDL App by Voice Recognition (pushing Push To Talk (PTT) button).
</ol>

### [2.3. Launch by choosing Audio source on the Audio screen](./2.3.Launch_by_choosing_the_Audio_source_on_the_Audio_screen/index.md)
<ol>
This chapter describes the launch of the SDL App from the Audio source selection screen.
The icon of SDL Media App which AppHMIType is "MEDIA" will be displayed on the audio source selection screen.
</ol>

### [2.4. Launch by Hard SW](./2.4.Launch_by_Hard_SW/index.md)
<ol>
This chapter describes the launch of SDL App by pressing the Hard SW.
Some systems do not have the Hard SW, and set it as the Software SW in a fixed area on the screen.
In this chapter, all mentioned above will be treated synonymously as the Hard SW.
</ol>

### [2.5. Behavior after ACC OFFON(Resume of Last Launched SDL App)](./2.5.Behavior_after_ACC_OFFON(Resume_of_Last_Launched_SDL_App)/index.md)
<ol>
This chapter describes the behavior of the SDL App after ACC OFF/ON.
</ol>

### [2.6. Change Screen to SDL App from Other Smartphone Linkage Functions](./2.6.Change_Screen_to_SDL_App_from_Other_Smartphone_Linkage_Functions/index.md)
<ol>
This chapter describes the method to switch between the SDL App and other smartphone linkage functions(CarPlay (CP)/ Android Auto(AA)).
</ol>

### [3. Switching SDL Apps](./3.Switching_SDL_Apps/index.md)
<ol>
This chapter describes the sequence of switching SDL Apps.
</ol>

### [4.1. Switching Communication Paths](./4.1.Switching_Communication_Paths/index.md)
<ol>
This chapter describes the priority rule and status notification to switch connections in case of the system connects a device via multiple paths.
</ol>

### [4.2. WiFi Connection](./4.2.WiFi_Connection/index.md)
<ol>
This chapter describes about WiFi connection.
</ol>

### [5. SDL App Interruption](./5.SDL_App_Interruption/index.md)
<ol>
This chapter describes the interruption of SDL App.
Listed below are three cases considered as kinds of interruption.
<ol>
- Interruption from SDL App while SDL App is running<br>
- Interruption from SDL App while Native is running<br>
- Interruption from Native while SDL App is running
</ol>
</ol>

### [6. Behavior of each HMI Status](./6.Behavior_of_each_HMI_Status/index.md)
<ol>
This chapter provides information about the definition of the HMI Status (Level) and the behavior of SDL Apps by HMI status.
The HU controls the status of display and playback audio by notifying the HMI Level to the mobile.
</ol>

### [Appendix. Terminologies and Definitions](./Appendix.Terminologies_and_Definitions/index.md)