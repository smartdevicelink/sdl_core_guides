# Migrating SDL Core 6.1 to 7.0

The 7.0 release had a number of changes and additions to the HMI API that will require updates to your SDL Core integration in your head unit. 

## Environment Update

The minimum environment requirements have changed for Ubuntu 18. GCC Version 7.5.x is now recommended over the previously recommended GCC Version 7.3.x.

## Breaking Changes

- The `url` parameter in the HMI API and RPC Spec has removed its max length requirement.

- The parameter `appID` has been removed from VehicleInfo.UnsubscribeVehicleData request to the HMI.

## Newly Deprecated

### Deprecated Character Sets

Character sets `TYPE2SET`, `TYPE5SET`, `CID1SET`, and `CID2SET` have been deprecated. These character sets only had proprietary significance and HMIs can now choose from the following character sets:

- ASCII
- ISO_8859_1
- UTF_8

### Deprecated HMI RPC: OnFindApplications

This unimplemented RPC has been marked as deprecated and should be removed in the next major version change of SDL Core. 

### Deprecated Vehicle Data

Vehicle Data parameters `fuelLevel` and `fuelLevel_state` have been deprecated. Please make updates to use expanded vehicle data struct `FuelRange`.

```
<struct name="FuelRange">
    <param name="type" type="Common.FuelType" mandatory="false"/>
    <param name="range" type="Float" minvalue="0" maxvalue="10000" mandatory="false">
        <description>
            The estimate range in KM the vehicle can travel based on fuel level and consumption.
        </description>
    </param>
    <param name="level" type="Float" minvalue="-6" maxvalue="1000000" mandatory="false">
    <description>The relative remaining capacity of this fuel type (percentage).</description>
    </param>
    <param name="levelState" type="Common.ComponentVolumeStatus" mandatory="false">
        <description>The fuel level state</description>
    </param>
    <param name="capacity" type="Float" minvalue="0" maxvalue="1000000" mandatory="false">
    <description>The absolute capacity of this fuel type.</description>
    </param>
    <param name="capacityUnit" type="Common.CapacityUnit" mandatory="false">
    <description>The unit of the capacity of this fuel type such as liters for gasoline or kWh for batteries.</description>
    </param>
</struct>
```

Vehicle Data parameter `prndl` has been deprecated. Please make updates to use the new vehicle data struct `GearStatus`.

```
<struct name="GearStatus">
    <param name="userSelectedGear" type="Common.PRNDL" mandatory="false">
        <description>Gear position selected by the user i.e. Park, Drive, Reverse</description>
    </param>
    <param name="actualGear" type="Common.PRNDL" mandatory="false">
        <description>Actual Gear in use by the transmission</description>
    </param>
    <param name="transmissionType" type="Common.TransmissionType" mandatory="false">
        <description>Tells the transmission type</description>
    </param>
</struct>
```

## Additions

### Vehicle Data

- FuelRange was expanded to replace `fuelLevel` and `fuelLevel_state` parameters
- New vehicle data type: GearStatus to replace `prndl` parameter
- New vehicle data type: `StabilityControlStatus`
- New vehicle data type: `WindowStatus`
- New vehicle data type: `HandsOffSteering`

It is not required to implement all vehicle data types. If a type is unsupported by your headunit, please be sure to respond to SDL Core with result `UNSUPPORTED_RESOURCE` if an unsupported request has been made.

### HMI UI Additions

#### Menu Changes

SDL Core 7.0 adds extended capabilities to the app menu. SDL Core now supports nested submenus, dynamic menus, and menu browsing limitations while driver distraction mode is enabled. 

Nested Submenus:

An app can now request to add a submenu to another submenu by specifying a `parentID`. This did not require any new parameters on the HMI side. HMIs should be updated to process `parentID` in an `AddSubMenu` request. This param was previously only reserved for `AddCommands`. 

```
<function name="AddSubMenu" messagetype="request">
...
    <param name="menuParams" type="Common.MenuParams" mandatory="true">
        <description>Position, parentID, and name of menu to be added.</description>
    </param>
...
```

Dynamic Menus:

Two new RPCs were added to HMI API: UI.OnUpdateFile and UI.OnUpdateSubmenu. UI.OnUpdateFile request allows the HMI to request images from an SDL connected app when needed in an effort to reduce the amount of data an app needs to save on the head unit. UI.OnUpdateSubmenu request allows the HMI to dynamically request when submenu information is populated by the app. This functionality helps reduce the system load when an app first connects as the app is not required to load all menu contents onto the head unit immediately.

```
<function name="OnUpdateFile" messagetype="notification">
    <description>For the HMI to tell Core that a file needs to be retrieved from the app.</description>
    <param name="appID" type="Integer" mandatory="true">
    <description>ID of application related to this RPC.</description>
    </param>
    <param name="fileName" type="String" maxlength="255" mandatory="true">
    <description>File reference name.</description>
    </param>
</function>

<function name="OnUpdateSubMenu" messagetype="notification">
    <description>For the HMI to tell Core that a submenu needs updating</description>

    <param name="appID" type="Integer" mandatory="true">
    <description>ID of application related to this RPC.</description>
    </param>

    <param name="menuID" type="Integer" minvalue="0" maxvalue="2000000000" mandatory="true">
    <description>This menuID must match a menuID in the current menu structure</description>
    </param>

    <param name="updateSubCells" type="Boolean" mandatory="false">
    <description>If not set, assume false. If true, the app should send AddCommands with parentIDs matching the menuID. These AddCommands will then be attached to the submenu and displayed if the submenu is selected.</description>
    </param>
</function>
```

Dynamic menus are optional, and the HMI's ability to support this feature is designated by the `DynamicUpdateCapabilities` struct.

```
<struct name="DynamicUpdateCapabilities">
    <param name="supportedDynamicImageFieldNames" type="ImageFieldName" array="true" mandatory="false" minsize="1">
    <description>An array of ImageFieldName values for which the system supports sending OnFileUpdate notifications. If you send an Image struct for that image field with a name without having uploaded the image data using PutFile that matches that name, the system will request that you upload the data with PutFile at a later point when the HMI needs it. The HMI will then display the image in the appropriate field. If not sent, assume false.</description>
    </param>

    <param name="supportsDynamicSubMenus" type="Boolean" mandatory="false">
    <description>If true, the head unit supports dynamic sub-menus by sending OnUpdateSubMenu notifications. If true, you should not send AddCommands that attach to a parentID for an AddSubMenu until OnUpdateSubMenu is received with the menuID. At that point, you should send all AddCommands with a parentID that match the menuID. If not set, assume false.</description>
    </param>
</struct>

```

Driver Distraction Limitations:

An HMI integration may choose to limit the amount of data available to the user when driver distraction is enabled. These limations include setting a limit on the number of menu items shown to the user in a given view, as well as setting a limit on how deep a user can drill down into nested submenus. This HMI capability is communicated to SDL Core and connected apps via the `DriverDistractionCapability` struct.

Setting these limits does not change the behavior of SDL Core. It is up to the HMI's integration to honor the designated limits and control how much information is available to the user.

```
<struct name="DriverDistractionCapability">
    <param name="menuLength" type="Integer" mandatory="false">
        <description>The number of items allowed in a Choice Set or Command menu while the driver is distracted</description>
    </param>
    <param name="subMenuDepth" type="Integer" minvalue="1" mandatory="false">
        <description>The depth of submenus allowed when the driver is distracted. e.g. 3 == top level menu -> submenu -> submenu; 1 == top level menu only</description>
    </param>
</struct>
```

#### New UI Component: Subtle Alert

`SubtleAlert` RPC was added as a less intrusive UI notification when compared to the `Alert` RPC. The `OnSubtleAlertPressed` notification was also added as a way for mobile apps to be aware of and optionally take action when a user clicks on a `SubtleAlert` notification.

```
<function name="SubtleAlert" messagetype="request">
    <description>Request from SDL to show a subtle alert message on the display.</description>
    <param name="alertStrings" type="Common.TextFieldStruct" mandatory="true" array="true" minsize="0" maxsize="2">
        <description>Array of lines of alert text fields. See TextFieldStruct. Uses subtleAlertText1, subtleAlertText2.</description>
    </param>
    <param name="alertIcon" type="Common.Image" mandatory="false">
        <description>
            Image to be displayed for the corresponding alert. See Image. 
            If omitted, no (or the default if applicable) icon should be displayed.
        </description>
    </param>
    <param name="duration" type="Integer" mandatory="false" minvalue="3000" maxvalue="10000">
        <description>Timeout in milliseconds.</description>
    </param>
    <param name="softButtons" type="Common.SoftButton" mandatory="false" minsize="0" maxsize="2" array="true">
        <description>App defined SoftButtons</description>
    </param>
    <param name="alertType" type="Common.AlertType" mandatory="true">
        <description>Defines if only UI or BOTH portions of the Alert request are being sent to HMI Side</description>
    </param>
    <param name="appID" type="Integer" mandatory="true">
        <description>ID of application requested this RPC.</description>
    </param>
    <param name="cancelID" type="Integer" mandatory="false">
        <description>
            An ID for this specific alert to allow cancellation through the `CancelInteraction` RPC.
        </description>
    </param>
</function>

<function name="SubtleAlert" messagetype="response">
    <param name="tryAgainTime" type="Integer" mandatory="false" minvalue="0" maxvalue="2000000000">
        <description>Amount of time (in milliseconds) that SDL must wait before resending an alert. Must be provided if another system event or overlay currently has a higher priority than this alert.</description>
    </param>
</function>

<function name="OnSubtleAlertPressed" messagetype="notification">
    <description>
    Sent when the alert itself is touched (outside of a soft button). Touching (or otherwise selecting) the alert should open the app before sending this notification.
    </description>
    <param name="appID" type="Integer" mandatory="true">
        <description>ID of application that is related to this RPC.</description>
    </param>
</function>
```

### Webengine Projection Support

A new `WEB_VIEW` `AppHMIType` and template layout was added which will allow apps to render a template-independent view in a browser environment with JavaScript and HTML.

```
<enum name="AppHMIType">
  <description>Enumeration listing possible app types.</description>
...
   <element name="WEB_VIEW" />
</enum>
```

This `AppHMIType` must be explicitly specified in an app's policy table entry for SDL Core to allow it to be used.

Policy Table Entry:

```
...
"app_policies": {
    "webengine_appID": {
+       "AppHMIType": ["WEB_VIEW"],
        "keep_context": false,
        "steal_focus": false,
        "priority": "NONE",
        "default_hmi": "NONE",
        "groups": [
            "Base-4"
        ],
        "RequestType": [],
        "RequestSubType": []
    },
...
```



### New UI.GetCapabilities parameter: pcmStreamCapabilities

This parameter was added to the HMI API to align better with the Mobile API.

```
<function name="GetCapabilities" messagetype="response">
	<param name="displayCapabilities" type="Common.DisplayCapabilities" mandatory="true">
		<description>Information about the capabilities of the display: its type, text field supported, etc. See DisplayCapabilities. </description>
	</param>
	<param name="audioPassThruCapabilities" type="Common.AudioPassThruCapabilities" mandatory="true"/>
	<param name="hmiZoneCapabilities" type="Common.HmiZoneCapabilities" mandatory="true"/>
	<param name="softButtonCapabilities" type="Common.SoftButtonCapabilities" minsize="1" maxsize="100" array="true" mandatory="false">
		<description>Must be returned if the platform supports on-screen SoftButtons.</description>
	</param>
	<param name="hmiCapabilities" type="Common.HMICapabilities" mandatory="false">
		<description>Specifies the HMI’s capabilities. See HMICapabilities.</description>
	</param>
	<param name="systemCapabilities" type="Common.SystemCapabilities" mandatory="false">
		<description>Specifies system capabilities. See SystemCapabilities</description>
	</param>
+	<param name="pcmStreamCapabilities" type="Common.AudioPassThruCapabilities" mandatory="false"/>
</function>
```
