# 1.5. Definitions when the HU returns RPC Error

## 1. Overview
This chapter describes the RPC in MOBILE_API.xml and HMI_API.xml that has a lack of or incorrect description.
In addition, we have modified the incorrect definition and complemented the missing details on these RPC parameters.

## 2. Background/Purpose/Reason for Standardization
There is a lack of or incorrect description in the current RPC documents of MOBILE_API.xml and HMI_API.xm.
Hence, the purpose of this document is to standardize such issue using the results of operations confirmed from source code, etc, in order to be able to contribute to the SDL Ecosystem.

## 3. Function Details
### 3.1. Lack of definition in the RPC parameter
Below is RPC parameter description in MOBILE_API.xml that are added/modified.

(1) HMILevel (Type : enum)<br>
The enum value is defined because it was not included in the current definition.

```xml
 <enum name="HMILevel" since="1.0">
     <description>Enumeration that describes current levels of HMI.</description>

     <element name="FULL" internal_name="HMI_FULL">
+        <description>
+            The application has full use of the SDL HMI. The app can output via TTS, display, or streaming audio and receive notifications via VR, Menu, and button presses.
+        </description>
     </element>

     <element name="LIMITED" internal_name="HMI_LIMITED" >
+        <description>
+            This HMI Level is only defined for a media application using an HMI with an 8 inch touchscreen(Navi) system.
+            The application can display text by "Show" and receives button presses from media-oriented buttons(SEEKRIGHT, SEEKLEFT, TUNEUP, TUNEDOWN, PRESET_0-9).
+        </description>
     </element>

     <element name="BACKGROUND" internal_name="HMI_BACKGROUND" >
+        <description>
+            The application cannot interact with user via TTS, VR, Display or Button Presses.
+        </description>
     </element>

     <element name="NONE" internal_name="HMI_NONE" >
+        <description>
+            The application has been discovered by SDL, but the app cannot send any requests or receive any notifications.
+        </description>
     </element>
 </enum>
```

### 3.2. Incorrect definition in the RPC parameter
Below are RPC parameters description in HMI_API.xml that are modified.

(1) Show (Type : request)<br>
mainfield3 and mainField4 are added because they are also affected by the parameter.

```xml
 <function name="Show" messagetype="request">
     ...
     <param name="alignment" type="Common.TextAlignment" mandatory="false">
-        <description>Specifies how mainField1 and mainField2 texts should be aligned on the display.</description>
-        <description>If omitted, texts must be centered</description>
+        <description>
+            Specifies how mainField1, mainField2, mainField3 and mainField4 texts should be aligned on display. If omitted, texts will be centered.
+        </description>
     </param>
     ...
 </function>
```

Add description in case of the parameter is omitted.
If the parameter is omitted custom preset does not change, same as other Show parameter like "alignment" above.

```xml
 <function name="Show" messagetype="request">
     ...
     <param name="customPresets" type="String" maxlength="500" minsize="0" maxsize="10" array="true" mandatory="false">
         <description> App labeled on-screen presets (i.e. GEN3 media presets or dynamic search suggestions).</description>
-        <description>If omitted on supported displays, the presets will be shown as not defined.</description>
+        <description>
+            If omitted on supported displays, the presets will not change.
+        </description>
     </param>
     ...
 </function>
```

(2) PerformInteraction (Type : request)<br>
The default value is added because it was not described in the "interactionLayout" parameter.

```xml
 <function name="PerformInteraction" messagetype="request">
     ...
     <param name="interactionLayout" type="Common.LayoutMode" mandatory="false">
-        <description>See LayoutMode.</description>
+        <description>
+            See LayoutMode. If omitted on supported displays, the value is set to "ICON_ONLY".
+        </description>
     </param>
     ...
</function>
```

### 3.3. RPC Invalid Data Error
Below are RPCs that returns an "error: Invalid Data" if the length of the string is 0.
The definition of minlength is added because it was not included in the current definition.

#### 1. RPC Invalid data error notification items (HMI_API .xml)
(1) Choice (Type : struct)

```xml
 <struct name="Choice">
     ...
-    <param name="menuName" type="String" maxlength="500" mandatory="false">
+    <param name="menuName" type="String" minlength="1" maxlength="500" mandatory="false">
         <description> The name of the choice </description>
     </param>
     ...
-    <param name="secondaryText" maxlength="500" type="String" mandatory="false">
+    <param name="secondaryText" minlength="1" maxlength="500" type="String" mandatory="false">
         <description> Optional secondary text to display; e.g. address of POI in a search result entry</description>
     </param>

-    <param name="tertiaryText" maxlength="500" type="String" mandatory="false">
+    <param name="tertiaryText" minlength="1" maxlength="500" type="String" mandatory="false">
         <description> Optional tertiary text to display; e.g. distance to POI for a search result entry</description>
     </param>
     ...
 </struct>
```

(2) VrHelpItem (Type : struct)

```xml
 <struct name="VrHelpItem">
-    <param name="text" maxlength="500" type="String" mandatory="true">
+    <param name="text" minlength="1" maxlength="500" type="String" mandatory="true">
         <description>Text to display for VR Help item</description>
     </param>
     ...
 </struct>
```

(3) SeatControlCapabilities (Type : struct)

```xml
 <struct name="SeatControlCapabilities">
-    <param name="moduleName" type="String" maxlength="100"  mandatory="true">
+    <param name="moduleName" type="String" minlength="1" maxlength="100"  mandatory="true">
         <description>
         The short friendly name of the light control module.
         It should not be used to identify a module by mobile application.
         </description>
     </param>
     ...
 </struct>
```

(4) MenuParams (Type : struct)

```xml
  <struct name="MenuParams">
-    <param name="menuName" type="String" maxlength="500" mandatory="true">
+    <param name="menuName" type="String" minlength="1" maxlength="500" mandatory="true">
         <description>The name of the sub menu/command.</description>
     </param>
     ...
 </struct>
```

(5) TextFieldStruct (Type : struct)

```xml
 <struct name="TextFieldStruct">
     ...
-    <param name="fieldText" type="String" maxlength="500" mandatory="true">
+    <param name="fieldText" type="String" minlength="1" maxlength="500" mandatory="true">
         <description>The  text itself.</description>
     </param>
     ...
 </struct>
```

(6) ButtonPress (Type : request)

```xml
 <function name="ButtonPress" messagetype="request">
     ...
-    <param name="moduleId" type="String" maxlength="100" mandatory="false">
+    <param name="moduleId" type="String" minlength="1" maxlength="100" mandatory="false">
         <description>Id of a module, published by System Capability. </description>
     </param>
     ...
 </function>
```

(7) DialNumber (Type : request)

```xml
 <function name="DialNumber" messagetype="request">
     ...
-    <param name="number" type="String" maxlength="40" mandatory="true">
+    <param name="number" type="String" minlength="1" maxlength="40" mandatory="true">
         <description>
             Phone number is a string, which can be up to 40 chars.
             All characters shall be stripped from string except digits 0-9 and * # , ; +
         </description>
     </param>
     ...
 </function>
```

(8) SetDisplayLayout (Type : request)

```xml
 <function name="SetDisplayLayout" messagetype="request">
     ...
-    <param name="displayLayout" type="String" maxlength="500" mandatory="true">
+    <param name="displayLayout" type="String" minlength="1" maxlength="500" mandatory="true">
         <description>
             Predefined or dynamically created screen layout.
             Currently only predefined screen layouts are defined.
         </description>
     </param>
     ...
 </function>
```

(9) ChangeRegistration (Type : request)

```xml
 <function name="ChangeRegistration" messagetype="request">
     ...
-    <param name="vrSynonyms" type="String" maxlength="40" minsize="1" maxsize="100" array="true" mandatory="false">
+    <param name="vrSynonyms" type="String" minlength="1" maxlength="40" minsize="1" maxsize="100" array="true"  mandatory="false">
         <description>
             Request new VR synonyms registration
             Defines an additional voice recognition command.
             Must not interfere with any name of previously registered applications from the same device.
         </description>
     </param>
     ...
 </function>
```

(10) SystemRequest (Type : request)

```xml
 <function name="SystemRequest" messagetype="request">
     ...
-    <param name="requestSubType" type="String" maxlength="255" mandatory="false">
+    <param name="requestSubType" type="String" minlength="1" maxlength="255" mandatory="false">
         <description>
             This parameter is filled for supporting OEM proprietary data exchanges.
         </description>
     </param>
     ...
 </function>
```

(11) Slider (Type : request)

```xml
 <function name="Slider" messagetype="request">
     ...
-    <param name="sliderHeader" type="String" maxlength="500" mandatory="true">
+    <param name="sliderHeader" type="String" minlength="1" maxlength="500" mandatory="true">
         <description>Text header to be displayed.</description>
     </param>

-    <param name="sliderFooter" type="String" maxlength="500"  minsize="1" maxsize="26" array="true" mandatory="false">
+    <param name="sliderFooter" type="String" minlength="1" maxlength="500" minsize="1" maxsize="26" array="true" mandatory="false">
         <description>Text footer to be displayed (meant to display min/max threshold descriptors).</description>
         <description>For a static text footer, only one footer string shall be provided in the array.</description>
         <description>For a dynamic text footer, the number of footer text string in the array must match the numTicks value.</description>
         <description>For a dynamic text footer, text array string should correlate with potential slider position index.</description>
         <description>If omitted on supported displays, no footer text shall be displayed.</description>
     </param>
     ...
 </function>
```

(12) SendLocation (Type : request)

```xml
 <function name="SendLocation" messagetype="request">
     ...
-    <param name="locationName" type="String" maxlength="500" mandatory="false">
+    <param name="locationName" type="String" minlength="1" maxlength="500" mandatory="false">
         <description>     Name / title of intended location   </description>
     </param>

-    <param name="locationDescription" type="String" maxlength="500" mandatory="false">
+    <param name="locationDescription" type="String" minlength="1" maxlength="500" mandatory="false">
         <description>        Description intended location / establishment (if applicable)   </description>
     </param>

-    <param name="addressLines" type="String" maxlength="500" minsize="0" maxsize="4" array="true" mandatory="false">
+    <param name="addressLines" type="String" minlength="1" maxlength="500" minsize="0" maxsize="4" array="true" mandatory="false">
         <description>     Location address (if applicable)   </description>
     </param>

-    <param name="phoneNumber" type="String" maxlength="500" mandatory="false">
+    <param name="phoneNumber" type="String" minlength="1" maxlength="500" mandatory="false">
         <description>     Phone number of intended location / establishment (if applicable)   </description>
     </param>
     ...
 </function>
```

(13) SeatMemoryAction (Type : struct)

```xml
 <struct name="SeatMemoryAction">
     ...
-    <param name="label" type="String" maxlength="100" mandatory="false"/>
+    <param name="label" type="String" minlength="1" maxlength="100" mandatory="false"/>
     ...
 </struct>
```

(14) Image (Type : struct)

```xml
 <struct name="Image">
-    <param name="value" maxlength="65535" type="String" mandatory="true">
+    <param name="value" minlength="1" maxlength="65535" type="String" mandatory="true">
         <description>The path to the dynamic image stored on HU or the static binary image itself.</description>
     </param>
     ...
 </struct>
```

(15) TTSChunk (Type : struct)

```xml
 <struct name="TTSChunk">
     ...
-    <param name="text" type="String" mandatory="true" maxlength="500">
+    <param name="text" type="String" mandatory="true" minlength="1" maxlength="500">
         <description>The text or phonemes to be spoken, or the name of an audio file to play.</description>
     </param>
     ...
 </struct>
```

(16) EqualizerSettings (Type : struct)

```xml
 <struct name="EqualizerSettings">
     ...
-    <param name="channelName" type="String" mandatory="false" maxlength="50">
+    <param name="channelName" type="String" mandatory="false" minlength="1" maxlength="50">
         <description>read-only channel / frequency name (e.i. "Treble, Midrange, Bass" or "125 Hz")</description>
     </param>
     ...
 </struct>
```

(17) ModuleData (Type : struct)

```xml
 <struct name="ModuleData">
     ...
-    <param name="moduleId" type="String" maxlength="100" mandatory="false">
+    <param name="moduleId" type="String" minlength="1" maxlength="100" mandatory="false">
         <description>Id of a module, published by System Capability. </description>
     </param>
     ...
 </struct>
```

(18) CreateWindow (Type : request)

```xml
 <function name="CreateWindow" messagetype="request">
     ...
-    <param name="windowName" type="String" maxlength="100" mandatory="true">
+    <param name="windowName" type="String" minlength="1" maxlength="100" mandatory="true">
         <description>
             The window name to be used by the HMI. The name of the pre-created default window will match the app name.
             Multiple apps can share the same window name except for the default main window.
             Creating a window with a name which is already in use by the app will result in `DUPLICATE_NAME`.
         </description>
     </param>
     ...
 </function>
```

(19) GetInteriorVehicleData (Type : request)

```xml
 <function name="GetInteriorVehicleData" messagetype="request">
     ...
-    <param name="moduleId" type="String" maxlength="100" mandatory="false">
+    <param name="moduleId" type="String" minlength="1" maxlength="100" mandatory="false">
         <description>Id of a module, published by System Capability. </description>
     </param>
     ...
 </function>
```

(20) GetInteriorVehicleDataConsent (Type : request)

```xml
 <function name="GetInteriorVehicleDataConsent" messagetype="request">
     ...
-    <param name="moduleIds" type="String" maxlength="100" array="true" mandatory="false">
+    <param name="moduleIds" type="String" minlength="1" maxlength="100" array="true" mandatory="false">
         <description>Ids of a module, published by System Capability. </description>
     </param>
     ...
 </function>
```

#### 2. RPC Invalid data error notification items (MOBILE_API .xml)
(1) CloudAppProperties (Type : struct)

```xml
 <struct name="CloudAppProperties" since="5.1">
     ...
-    <param name="appID" type="String" maxlength="100" mandatory="true"/>
+    <param name="appID" type="String" minlength="1" maxlength="100" mandatory="true"/>
     ...
-    <param name="authToken" type="String" maxlength="65535" mandatory="false">
+    <param name="authToken" type="String" minlength="1" maxlength="65535" mandatory="false">
         <description>Used to authenticate websocket connection on app activation</description>
     </param>

-    <param name="cloudTransportType" type="String" maxlength="100" mandatory="false">
+    <param name="cloudTransportType" type="String" minlength="1" maxlength="100" mandatory="false">
         <description>Specifies the connection type Core should use</description>
     </param>
     ...
-    <param name="endpoint" type="String" maxlength="65535" mandatory="false">
+    <param name="endpoint" type="String" minlength="1" maxlength="65535" mandatory="false">
         <description>Specifies the endpoint which Core will attempt to connect to when this app is selected</description>
     </param>
 </struct>
```

(2) Choice (Type : struct)

```xml
 <struct name="Choice" since="1.0">
     ...
-    <param name="menuName" type="String" maxlength="500"  mandatory="true"/>
+    <param name="menuName" type="String" minlength="1" maxlength="500"  mandatory="true"/>

-    <param name="vrCommands" type="String" minsize="1" maxsize="100" maxlength="99" array="true" mandatory="false" since="5.0">
+    <param name="vrCommands" type="String" minsize="1" maxsize="100" minlength="1" maxlength="99" array="true" mandatory="false" since="5.0">
     ...
     </param>
     ...
-    <param name="secondaryText" maxlength="500" type="String" mandatory="false" since="3.0">
+    <param name="secondaryText" minlength="1" maxlength="500" type="String" mandatory="false" since="3.0">
         <description>Optional secondary text to display; e.g. address of POI in a search result entry</description>
     </param>

-    <param name="tertiaryText" maxlength="500" type="String" mandatory="false" since="3.0">
+    <param name="tertiaryText" minlength="1" maxlength="500" type="String" mandatory="false" since="3.0">
         <description>Optional tertiary text to display; e.g. distance to POI for a search result entry</description>
     </param>
     ...
 </struct>
```

(3) VrHelpItem (Type : struct)

```xml
 <struct name="VrHelpItem" since="2.0">
-    <param name="text" maxlength="500" type="String" mandatory="true">
+    <param name="text" minlength="1" maxlength="500" type="String" mandatory="true">
         <description>Text to display for VR Help item</description>
     </param>
     ...
 </struct>
```

(4) AppInfo (Type : struct)

```xml
 <struct name="AppInfo" since="4.2">
     ...
-    <param name="appDisplayName" type="String" maxlength="100" mandatory="true">
+    <param name="appDisplayName" type="String" minlength="1" maxlength="100" mandatory="true">
         <description>The name displayed for the mobile application on the mobile device (can differ from the app name set in the initial RAI request).</description>
     </param>

-    <param name="appBundleID" type="String" maxlength="256" mandatory="true">
+    <param name="appBundleID" type="String" minlength="1" maxlength="256" mandatory="true">
         <description>The AppBundleID of an iOS application or package name of
         the Android application. This supports App Launch strategies for each platform.</description>
     </param>

-    <param name="appVersion" type="String" maxlength="256" mandatory="true">
+    <param name="appVersion" type="String" minlength="1" maxlength="256" mandatory="true">
         <description>Represents the build version number of this particular mobile app.</description>
     </param>

-    <param name="appIcon" type="String" maxlength="500" mandatory="false">
+    <param name="appIcon" type="String" minlength="1" maxlength="500" mandatory="false">
         <description>A file reference to the icon utilized by this app (simplifies the process of setting an app icon during app registration).</description>
     </param>
 </struct>
```

(5) MenuParams (Type : struct)

```xml
 <struct name="MenuParams" since="1.0">
     ...
-    <param name="menuName" type="String" maxlength="500" mandatory="true">
+    <param name="menuName" type="String" minlength="1" maxlength="500" mandatory="true">
         <description>Text to show in the menu for this sub menu.</description>
     </param>
 </struct>
```

(6) Turn (Type : struct)

```xml
 <struct name="Turn" since="2.0">
-    <param name="navigationText" type="String" maxlength="500" mandatory="false">
+    <param name="navigationText" type="String" minlength="1" maxlength="500" mandatory="false">
         <description>Individual turn text.  Must provide at least text or icon for a given turn.</description>
     </param>
     ...
 </struct>
```

(7) SeatMemoryAction (Type : struct)

```xml
 <struct name="SeatMemoryAction" since="5.0">
     ...
-    <param name="label" type="String" maxlength="100" mandatory="false"/>
+    <param name="label" type="String" minlength="1" maxlength="100" mandatory="false"/>
     ...
 </struct>
```

(8) KeyboardProperties (Type : struct)

```xml
 <struct name="KeyboardProperties" since="3.0">
     ...
-    <param name="limitedCharacterList" type="String" maxlength="1" minsize="1" maxsize="100" array="true" mandatory="false">
+    <param name="limitedCharacterList" type="String" minlength="1" maxlength="1" minsize="1" maxsize="100" array="true" mandatory="false">
         <description>Array of keyboard characters to enable.</description>
         <description>All omitted characters will be greyed out (disabled) on the keyboard.</description>
         <description>If omitted, the entire keyboard will be enabled.</description>
     </param>

-    <param name="autoCompleteText" type="String" maxlength="1000" mandatory="false" deprecated="true" since="6.0">
+    <param name="autoCompleteText" type="String" minlength="1" maxlength="1000" mandatory="false" deprecated="true" since="6.0">
     ...
     </param>

-    <param name="autoCompleteList" type="String" maxlength="1000" minsize="0" maxsize="100" array="true" mandatory="false" since="6.0">
+    <param name="autoCompleteList" type="String" minlength="1" maxlength="1000"  minsize="0" maxsize="100" array="true" mandatory="false" since="6.0"> 
         <description>
             Allows an app to prepopulate the text field with a list of suggested or completed entries as the user types. 
             If empty, the auto-complete list will be removed from the screen.
         </description>
     </param>
 </struct>
```

(9) LocationDetails (Type : struct)

```xml
 <struct name="LocationDetails" since="4.1">
     ...
-    <param name="locationName" type="String" maxlength="500" mandatory="false">
+    <param name="locationName" type="String" minlength="1" maxlength="500" mandatory="false">
         <description>Name of location.</description>
     </param>

-    <param name="addressLines" type="String" maxlength="500" minsize="0" maxsize="4" array="true" mandatory="false">
+    <param name="addressLines" type="String" minlength="1" maxlength="500" minsize="0" maxsize="4" array="true" mandatory="false">
         <description>Location address for display purposes only</description>
     </param>

-    <param name="locationDescription" type="String" maxlength="500" mandatory="false">
+    <param name="locationDescription" type="String" minlength="1" maxlength="500" mandatory="false">
         <description>Description intended location / establishment (if applicable)</description>
     </param>

-    <param name="phoneNumber" type="String" maxlength="500" mandatory="false">
+    <param name="phoneNumber" type="String" minlength="1" maxlength="500" mandatory="false">
         <description>Phone number of location / establishment.</description>
     </param>
     ...
 </struct>
```

(10) EqualizerSettings (Type : struct)

```xml
 <struct name="EqualizerSettings" since="5.0">
     ...
-    <param name="channelName" type="String" mandatory="false" maxlength="50">
+    <param name="channelName" type="String" mandatory="false" minlength="1" maxlength="50">
         <description>read-only channel / frequency name (e.i. "Treble, Midrange, Bass" or "125 Hz")</description>
     </param>
     ...
 </struct>
```

(11) ModuleData (Type : struct)

```xml
 <struct name="ModuleData" since="4.5">
     ...
-    <param name="moduleId" type="String" maxlength="100" mandatory="false" since="6.0">
+    <param name="moduleId" type="String" minlength="1" maxlength="100" mandatory="false" since="6.0">
         <description>Id of a module, published by System Capability. </description>
     </param>
     ...
 </struct>
```

(12) RegisterAppInterface (Type : request)

```xml
 <function name="RegisterAppInterface" functionID="RegisterAppInterfaceID" messagetype="request" since="1.0">
     ...
-    <param name="appName" type="String" maxlength="100" mandatory="true" since="1.0">
+    <param name="appName" type="String" minlength="1" maxlength="100" mandatory="true" since="1.0">
     ...
     </param>
     ...
-    <param name="ngnMediaScreenAppName" type="String" maxlength="100" mandatory="false" since="1.0">
+    <param name="ngnMediaScreenAppName" type="String" minlength="1" maxlength="100" mandatory="false" since="1.0">
     ...
     </param>

-    <param name="vrSynonyms" type="String" maxlength="40" minsize="1" maxsize="100" array="true" mandatory="false" since="1.0">
+    <param name="vrSynonyms" type="String" minlength="1" maxlength="40" minsize="1" maxsize="100" array="true" mandatory="false" since="1.0">
     ...
     </param>
     ...
-    <param name="hashID" type="String" maxlength="100" mandatory="false" since="3.0">
+    <param name="hashID" type="String" minlength="1" maxlength="100" mandatory="false" since="3.0">
     ...
     </param>
     ...
-    <param name="appID" type="String" maxlength="100" mandatory="true" since="2.0">
+    <param name="appID" type="String" minlength="1" maxlength="100" mandatory="true" since="2.0">
     ...
     </param>

-    <param name="fullAppID" type="String" maxlength="100" mandatory="false" since="5.0">
+    <param name="fullAppID" type="String" minlength="1" maxlength="100" mandatory="false" since="5.0">
         <description>ID used to validate app with policy table entries</description>
     </param>
     ...
 </function>
```

(13) CreateWindow (Type : request)

```xml
 <function name="CreateWindow" functionID="CreateWindowID" messagetype="request" since="6.0">
     ...
-    <param name="windowName" type="String" maxlength="100" mandatory="true">
+    <param name="windowName" type="String" minlength="1" maxlength="100" mandatory="true">
         <description>
             The window name to be used by the HMI. The name of the pre-created default window will match the app name.
             Multiple apps can share the same window name except for the default main window.
             Creating a window with a name which is already in use by the app will result in `DUPLICATE_NAME`.
         </description>
     </param>
     ...
 </function>
```

(14) SetGlobalProperties (Type : request)

```xml
 <function name="SetGlobalProperties" functionID="SetGlobalPropertiesID" messagetype="request" since="1.0">
     ...
-    <param name="vrHelpTitle" type="String" maxlength="500" mandatory="false" since="2.0">
+    <param name="vrHelpTitle" type="String" minlength="1" maxlength="500" mandatory="false" since="2.0">
         <description>
             VR Help Title text.
             If omitted on supported displays, the default module help title shall be used.
             If omitted and one or more vrHelp items are provided, the request will be rejected.
         </description>
     </param>
     ...
-    <param name="menuTitle" maxlength="500" type="String" mandatory="false" since="3.0">
+    <param name="menuTitle" minlength="1" maxlength="500" type="String" mandatory="false" since="3.0">
         <description>Optional text to label an app menu button (for certain touchscreen platforms).</description>
     </param>
 </function>
```

(15) AddCommand (Type : request)

```xml
 <function name="AddCommand" functionID="AddCommandID" messagetype="request" since="1.0">
     ...
-    <param name="vrCommands" type="String" minsize="1" maxsize="100" maxlength="99" array="true" mandatory="false">
+    <param name="vrCommands" type="String" minsize="1" maxsize="100" minlength="1" maxlength="99" array="true" mandatory="false">
         <description>
             An array of strings to be used as VR synonyms for this command.
             If this array is provided, it may not be empty.
         </description>
     </param>
     ...
 </function>
```

(16) AddSubMenu (Type : request)

```xml
 <function name="AddSubMenu" functionID="AddSubMenuID" messagetype="request" since="1.0">
     ...
-    <param name="menuName" maxlength="500" type="String" mandatory="true">
+    <param name="menuName" minlength="1" maxlength="500" type="String" mandatory="true">
         <description>Text to show in the menu for this sub menu.</description>
     </param>
     ...
 </function>
```

(17) PerformInteraction (Type : request)

```xml
 <function name="PerformInteraction" functionID="PerformInteractionID" messagetype="request" since="1.0">
     ...
-    <param name="initialText" type="String" maxlength="500"  mandatory="true">
+    <param name="initialText" type="String" minlength="1" maxlength="500"  mandatory="true">
         <description>
             Text to be displayed first.
         </description>
     </param>
     ...
 </function>
```

(18) Alert (Type : request)

```xml
 <function name="Alert" functionID="AlertID" messagetype="request" since="1.0">
     ...
-    <param name="alertText1" type="String" maxlength="500" mandatory="false">
+    <param name="alertText1" type="String" minlength="1" maxlength="500" mandatory="false">
         <description>The first line of the alert text field</description>
     </param>

-    <param name="alertText2" type="String" maxlength="500" mandatory="false">
+    <param name="alertText2" type="String" minlength="1" maxlength="500" mandatory="false">
         <description>The second line of the alert text field</description>
     </param>

-    <param name="alertText3" type="String" maxlength="500" mandatory="false" since="2.0">
+    <param name="alertText3" type="String" minlength="1" maxlength="500" mandatory="false" since="2.0">
         <description>The optional third line of the alert text field</description>
     </param>
     ...
 </function>
```

(19) Show (Type : request)

```xml
 <function name="Show" functionID="ShowID" messagetype="request" since="1.0">
     ...
-    <param name="customPresets" type="String" maxlength="500" minsize="0" maxsize="10" array="true" mandatory="false" since="3.0">
+    <param name="customPresets" type="String" minlength="1" maxlength="500" minsize="0" maxsize="10" array="true" mandatory="false" since="3.0">
         <description>
             App labeled on-screen presets (i.e. on-screen media presets or dynamic search suggestions).
             If omitted on supported displays, the presets will be shown as not defined.
         </description>
     </param>
     ...
 </function>
```

(20) ScrollableMessage (Type : request)

```xml
 <function name="ScrollableMessage" functionID="ScrollableMessageID" messagetype="request" since="2.0">
     ...
-    <param name="scrollableMessageBody" type="String" maxlength="500" mandatory="true">
+    <param name="scrollableMessageBody" type="String" minlength="1" maxlength="500" mandatory="true">
         <description>Body of text that can include newlines and tabs.</description>
     </param>
     ...
 </function>
```

(21) Slider (Type : request)

```xml
 <function name="Slider" functionID="SliderID" messagetype="request" since="2.0">
     ...
-    <param name="sliderHeader" type="String" maxlength="500" mandatory="true">
+    <param name="sliderHeader" type="String" minlength="1" maxlength="500" mandatory="true">
         <description>Text header to display</description>
     </param>

-    <param name="sliderFooter" type="String" maxlength="500"  minsize="1" maxsize="26" array="true" mandatory="false">
+    <param name="sliderFooter" type="String" minlength="1" maxlength="500"  minsize="1" maxsize="26" array="true" mandatory="false">
         <description>
             Text footer to display (meant to display min/max threshold descriptors).
             For a static text footer, only one footer string shall be provided in the array.
             For a dynamic text footer, the number of footer text string in the array must match the numTicks value.
             For a dynamic text footer, text array string should correlate with potential slider position index.
             If omitted on supported displays, no footer text shall be displayed.
         </description>
     </param>
     ...
 </function>
```

(22) ChangeRegistration (Type : request)

```xml
 <function name="ChangeRegistration" functionID="ChangeRegistrationID" messagetype="request" since="2.0">
     ...
-    <param name="appName" type="String" maxlength="100" mandatory="false" since="3.0">
+    <param name="appName" type="String" minlength="1" maxlength="100" mandatory="false" since="3.0">
         <description>Request new app name registration</description>
     </param>
     ...
-    <param name="ngnMediaScreenAppName" type="String" maxlength="100" mandatory="false" since="3.0">
+    <param name="ngnMediaScreenAppName" type="String" minlength="1" maxlength="100" mandatory="false" since="3.0">
         <description>Request new app short name registration</description>
     </param>

-    <param name="vrSynonyms" type="String" maxlength="40" minsize="1" maxsize="100" array="true" mandatory="false" since="3.0">
+    <param name="vrSynonyms" type="String" minlength="1" maxlength="40" minsize="1" maxsize="100" array="true" mandatory="false" since="3.0">
         <description>Request new VR synonyms registration</description>
     </param>
 </function>
```

(23) PutFile (Type : request)

```xml
 <function name="PutFile" functionID="PutFileID" messagetype="request" since="3.0">
     ...
-    <param name="syncFileName" type="String" maxlength="255" mandatory="true">
+    <param name="syncFileName" type="String" minlength="1" maxlength="255" mandatory="true">
         <description>File reference name.</description>
     </param>
     ...
 </function>
```

(24) GetFile (Type : request)

```xml
 <function name="GetFile" functionID="GetFileID" messagetype="request" since="5.1">
     ...
-    <param name="fileName" type="String" maxlength="255" mandatory="true">
+    <param name="fileName" type="String" minlength="1" maxlength="255" mandatory="true">
         <description>File name that should be retrieved</description>
     </param>
     ...
 </function>
```

(25) DeleteFile (Type : request)

```xml
 <function name="DeleteFile" functionID="DeleteFileID" messagetype="request" since="3.0">
     ...
-    <param name="syncFileName" type="String" maxlength="500" mandatory="true">
+    <param name="syncFileName" type="String" minlength="1" maxlength="500" mandatory="true">
         <description>File reference name.</description>
     </param>
 </function>
```

(26) SetAppIcon (Type : request)

```xml
 <function name="SetAppIcon" functionID="SetAppIconID" messagetype="request" since="3.0">
     ...
-    <param name="syncFileName" type="String" maxlength="500" mandatory="true">
+    <param name="syncFileName" type="String" minlength="1" maxlength="500" mandatory="true">
         <description>File reference name.</description>
     </param>
 </function>
```

(27) SetDisplayLayout (Type : request)

```xml
 <function name="SetDisplayLayout" functionID="SetDisplayLayoutID" messagetype="request" deprecated="true" since="6.0">
     ...
-    <param name="displayLayout" type="String" maxlength="500" mandatory="true">
+    <param name="displayLayout" type="String" minlength="1" maxlength="500" mandatory="true">
         <description>
             Predefined or dynamically created screen layout.
             Currently only predefined screen layouts are defined.
         </description>
     </param>
     ...
 </function>
```

(28) SystemRequest (Type : request)

```xml
 <function name="SystemRequest" functionID="SystemRequestID" messagetype="request" since="3.0">
     ...
-    <param name="requestSubType" type="String" maxlength="255" mandatory="false" since="5.0">
+    <param name="requestSubType" type="String" minlength="1" maxlength="255" mandatory="false" since="5.0">
         <description>
             This parameter is filled for supporting OEM proprietary data exchanges.
         </description>
     </param>

-    <param name="fileName" type="String" maxlength="255" mandatory="false">
+    <param name="fileName" type="String" minlength="1" maxlength="255" mandatory="false">
         <description>
             Filename of HTTP data to store in predefined system staging area.
             Mandatory if requestType is HTTP.
             PROPRIETARY requestType should ignore this parameter.
         </description>
     </param>
 </function>
```

(29) SendLocation (Type : request)

```xml
 <function name="SendLocation" functionID="SendLocationID" messagetype="request" since="3.0">
     ...
-    <param name="locationName" type="String" maxlength="500" mandatory="false">
+    <param name="locationName" type="String" minlength="1" maxlength="500" mandatory="false">
         <description>
             Name / title of intended location
         </description>
     </param>

-    <param name="locationDescription" type="String" maxlength="500" mandatory="false">
+    <param name="locationDescription" type="String" minlength="1" maxlength="500" mandatory="false">
         <description>
             Description intended location / establishment (if applicable)
         </description>
     </param>

-    <param name="addressLines" type="String" maxlength="500" minsize="0" maxsize="4" array="true" mandatory="false">
+    <param name="addressLines" type="String" minlength="1" maxlength="500" minsize="0" maxsize="4" array="true" mandatory="false">
         <description>
             Location address (if applicable)
         </description>
     </param>

-    <param name="phoneNumber" type="String" maxlength="500" mandatory="false">
+    <param name="phoneNumber" type="String" minlength="1" maxlength="500" mandatory="false">
         <description>
             Phone number of intended location / establishment (if applicable)
         </description>
     </param>
     ...
 </function>
```

(30) DialNumber (Type : request)

```xml
 <function name="DialNumber" functionID="DialNumberID" messagetype="request" since="3.0">
     <description>Dials a phone number and switches to phone application.</description>

-    <param name="number" type="String" maxlength="40" mandatory="true">
+    <param name="number" type="String" minlength="1" maxlength="40" mandatory="true">
         <description>
             Phone number is a string, which can be up to 40 chars.
             All characters shall be stripped from string except digits 0-9 and * # , ; +
         </description>
     </param>
 </function>
```

(31) ButtonPress (Type : request)

```xml
 <function name="ButtonPress" functionID="ButtonPressID" messagetype="request" since="4.5">
     ...
-    <param name="moduleId" type="String" maxlength="100" mandatory="false" since="6.0">
+    <param name="moduleId" type="String" minlength="1" maxlength="100" mandatory="false" since="6.0">
         <description>Id of a module, published by System Capability. </description>
     </param>
     ...
 </function>
```

(32) GetInteriorVehicleData (Type : request)

```xml
 <function name="GetInteriorVehicleData" functionID="GetInteriorVehicleDataID" messagetype="request" since="4.5">
     <param name="moduleType" type="ModuleType" mandatory="true">
     ...
-    <param name="moduleId" type="String" maxlength="100" mandatory="false" since="6.0">
+    <param name="moduleId" type="String" minlength="1" maxlength="100" mandatory="false" since="6.0">
         <description>Id of a module, published by System Capability. </description>
     </param>
     ...
 </function>
```

(33) GetInteriorVehicleDataConsent (Type : request)

```xml
 <function name="GetInteriorVehicleDataConsent" functionID="GetInteriorVehicleDataConsentID" messagetype="request" since="6.0">
     ...
-    <param name="moduleIds" type="String" maxlength="100" array="true" mandatory="true">
+    <param name="moduleIds" type="String" minlength="1" maxlength="100" array="true" mandatory="true">
         <description>Ids of a module of same type, published by System Capability. </description>
     </param>
 </function>
```

(34) ReleaseInteriorVehicleDataModule (Type : request)

```xml
 <function name="ReleaseInteriorVehicleDataModule" functionID="ReleaseInteriorVehicleDataModuleID" messagetype="request" since="6.0">
     ...
-    <param name="moduleId" type="String" maxlength="100" mandatory="false" since="5.1">
+    <param name="moduleId" type="String" minlength="1" maxlength="100" mandatory="false" since="5.1">
         <description>Id of a module, published by System Capability. </description>
     </param>
 </function>
```

(35) GetCloudAppProperties (Type : request)

```xml
 <function name="GetCloudAppProperties" functionID="GetCloudAppPropertiesID" messagetype="request" since="5.1">
     <description>
         RPC used to get the current properties of a cloud application
     </description>

-    <param name="appID" type="String" maxlength="100" mandatory="true"></param>
+    <param name="appID" type="String" minlength="1" maxlength="100" mandatory="true"></param> 
 </function>
```

## 4. Differences from SDL standard specification
The definitions of the RPC parameters that are modified/complemented in Function Details differ from the existing SDL standard specification.
