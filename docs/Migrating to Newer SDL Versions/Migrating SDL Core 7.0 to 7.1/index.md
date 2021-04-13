# Migrating SDL Core 7.0 to 7.1

The 7.1 release had a number of changes and additions to the HMI API that will require updates to your SDL Core integration in your head unit. 

## Environment Update

The default supported version was changed to Ubuntu 20. Recommended GCC Version 9.3.x.

Support was added for OpenSSL 1.1, we recommend updating your version of the library accordingly.
Along with support for OpenSSL 1.1, a configurable `SecurityLevel` field was added to the INI file. This value can be customized depending on the security requirements of your system (see the [OpenSSL documentation](https://www.openssl.org/docs/man1.1.0/man3/SSL_CTX_get_security_level.html) for a description of each security level)

## Newly Deprecated

### Deprecated SyncPData RPCs

- RPC request and response for `EncodedSyncPData` has been marked as deprecated.
- RPC notification `OnEncodedSyncPData` has been marked as deprecated.

### Deprecated UI params

- `TextFieldName` element `mediaClock` has been marked as deprecated.
- `Show` RPC param `mediaClock` has been marked as deprecated.
- `RegisterAppInterface` parameters `vehicleType` and `systemSoftwareVersion` has been marked as deprecated. Please make updates to use the parameters from the `StartService ACK` protocol message.

### Deprecated Functions

- The function `DynamicApplicationData::IsSubMenuNameAlreadyExist` has been marked as deprecated and should be removed in the next major version change of SDL Core. Please make updates to remove all uses of the function.

- The function `ApplicationManagerImpl::OnAppStreaming(uint32_t, protocol_handler::ServiceType, const Application::StreamingState)` has been marked as deprecated and should be removed in the next major version change of SDL Core. Please make updates to use the new function signature `ApplicationManagerImpl::OnAppStreaming(uint32_t, protocol_handler::ServiceType, bool)`.

- The function `ProtocolHandlerImpl::NotifySessionStarted(const SessionContext&, std::vector<std::string>&, const std::string)` has been marked as deprecated and should be removed in the next major version change of SDL Core. Please make updates to use the new function signature `ProtocolHandlerImpl::NotifySessionStarted(SessionContext&, std::vector<std::string>&, const std::string)`.

- The function `file_system::ConvertPathForURL` has been marked as deprecated and should be removed in the next major version change of SDL Core. Please make updates to remove all uses of the function.

### Deprecated Vehicle Data

- Vehicle Data parameter `externalTemperature` has been deprecated. Please make updates to use the new vehicle data struct `climateData`.

- Vehicle Data parameters `driverDoorAjar`, `passengerDoorAjar`, `rearLeftDoorAjar` and `rearRightDoorAjar` have been deprecated. Please make updates to use the new `doorStatuses` parameter.

## Additions

### Vehicle Data

- `BodyInformation` was expanded to replace `driverDoorAjar`, `passengerDoorAjar`, `rearLeftDoorAjar` and `rearRightDoorAjar` parameters.
- New vehicle data type: `climateData` to replace `externalTemperature` parameter.
- New vehicle data type: `seatOccupancy`.

It is not required to implement all vehicle data types. If a type is unsupported by your headunit, please be sure to respond to SDL Core with the result `UNSUPPORTED_RESOURCE` if an unsupported request has been made.

### HMI UI Additions

#### Custom Playback rates for SetMediaClockTimer

A media app now has the ability to specify a custom playback rate (ex. 125% speed) when setting the media playback timer and progress bar.

Added new parameter `countRate` to the `SetMediaClockTimer` RPC

```xml
<function name="SetMediaClockTimer" functionID="SetMediaClockTimerID" messagetype="request" since="1.0">
    <description>Sets the initial media clock value and automatic update method.</description>
        
    <!-- New Parameter -->
    <param name="countRate" type="Float" minvalue="0.1" maxvalue="100.0" defvalue="1.0" mandatory="false">
        <description>
        The value of this parameter is the amount that the media clock timer will advance per 1.0 seconds of real time.
        
        Values less than 1.0 will therefore advance the timer slower than real-time, while values greater than 1.0 will advance the timer faster than real-time.

        e.g. If this parameter is set to `0.5`, the timer will advance one second per two seconds real-time, or at 50% speed. If this parameter is set to `2.0`, the timer will advance two seconds per one second real-time, or at 200% speed.
        </description>
    </param>
</function>
```

#### Media Skip Indicators

A media app now has the ability to change the indicators for the `SEEKLEFT` and `SEEKRIGHT` buttons to show either time skip buttons or track skip buttons.

- Added new parameters `forwardSeekIndicator` and `backSeekIndicator` to the `SetMediaClockTimer` RPC.

```xml
<enum name="SeekIndicatorType">
    <element name="TRACK">
    <element name="TIME">
</enum>

<struct name="SeekStreamingIndicator">
    <description>
        The seek next / skip previous subscription buttons' content
    </description> 

    <param name="type" type="SeekIndicatorType" mandatory="true" />
    <param name="seekTime" type="Integer" minvalue="1" maxvalue="99" mandatory="false">
        <description>If the type is TIME, this number of seconds may be present alongside the skip indicator. It will indicate the number of seconds that the currently playing media will skip forward or backward.</description>
    </param>
</struct>

<function name="SetMediaClockTimer" messagetype="request">
  <!-- Additions -->
  <param name="forwardSeekIndicator" type="SeekStreamingIndicator" mandatory="false" />
  <param name="backSeekIndicator" type="SeekStreamingIndicator" mandatory="false" />
</function>
```

#### Main Menu UI updates

SDL Core 7.1 adds extended capabilities to the `AddSubMenu` and `AddCommand` RPCs. Both `AddSubmenu` and `AddCommand` now have additional optional textfields as well as an optional secondary image.

AddSubmenu:

```xml
<function name="AddSubMenu" functionID="AddSubMenuID" messagetype="request">
    <description>Adds a sub menu to the in-application menu.</description>
    
    <!-- New Parameters -->
    <param name="secondaryText" maxlength="500" type="String" mandatory="false">
        <description>Optional secondary text to display</description>
    </param>
    <param name="tertiaryText" maxlength="500" type="String" mandatory="false">
        <description>Optional tertiary text to display</description>
    </param>
    <param name="secondaryImage" type="Image" mandatory="false">
        <description>Optional secondary image struct for sub-menu cell</description>
    </param>
</function>
```

AddCommand:

```xml
<function name="AddCommand" functionID="AddCommandID" messagetype="request">
    <description>
        Adds a command to the in application menu.
        Either menuParams or vrCommands must be provided.
    </description>
    
    <!-- New Parameters -->
    <param name="secondaryImage" type="Image" mandatory="false">
        <description>Optional secondary image struct for menu cell</description>
    </param>
</function>

<struct name="MenuParams" since="1.0">
    <!-- New Parameters -->
    <param name="secondaryText" maxlength="500" type="String" mandatory="false">
        <description>Optional secondary text to display</description>
    </param>
    <param name="tertiaryText" maxlength="500" type="String" mandatory="false">
        <description>Optional tertiary text to display</description>
    </param>
</struct>
```

#### Broadening choice uniqueness

Prior to SDL Core 7.1, choice set choices and menu commands were required to have unique primary text. SDL Core 7.1 removes this restriction.

#### Keyboard Enhancements

SDL Core 7.1 adds a new `NUMERIC` keyboard layout and new enhancements to allow apps to mask entered characters and change special characters shown on the keyboard layout.

### OEM exclusive apps support

SDL Core 7.1 adds the ability to share vehicle type information before sending the Register App interface request. This will enable SDL adopters to provide exclusive apps to their users depending on vehicle type

The vehicle type information parameters have been added to the BSON payload of the `StartServiceACK` protocol message

|Tag Name|Type|Description|
|--------|----|-----------|
|make|String|Vehicle make|
|model|String|Vehicle model|
|modelYear|String|Vehicle model year|
|trim|String|Vehicle trim|
|systemSoftwareVersion|String|Vehicle system software version|
|systemHardwareVersion|String|Vehicle system hardware version|

The vehicle type information parameters (`vehicleType` and `systemSoftwareVersion`) in `RegisterAppInterface` have been deprecated in favor of these additions

### Video streaming capability updates

#### Preferred FPS

- Added new parameter `preferredFPS` to the `VideoStreamingCapability` struct.

```xml
<struct name="VideoStreamingCapability" since="4.5">
    <description>Contains information about this system's video streaming capabilities.</description>
    ...
    <!-- new param -->
    <param name="preferredFPS" type="Integer" minvalue="0" maxvalue="2147483647" mandatory="false">
        <description>The preferred frame rate per second of the head unit. The mobile application / app library may take other factors into account that constrain the frame rate lower than this value, but it should not perform streaming at a higher frame rate than this value.</description>
    </param>
</struct>
```

#### Updating video streaming capabilities during ignition cycle

SDL Core 7.1 adds the ability for an application to update its video streaming capabilities during the ignition cycle. This will allow SDL to handle uses cases that require dynamic resolution switching (Picture-in-Picture, preview, split-screen, etc.)

- Added new parameter `additionalVideoStreamingCapabilities` to the `VideoStreamingCapability` struct.

```xml
<struct name="VideoStreamingCapability" since="4.5">
    <!-- Existing params -->
    <param name="additionalVideoStreamingCapabilities" type="VideoStreamingCapability" array="true" minvalue="1" maxvalue="100" mandatory="false" since="7.1">
    </param>
</struct>
```

- Added new RPC notification `OnAppCapabilityUpdated` which can be sent by an app, as well as related structs `AppCapability` and `AppCapabilityType`.

```xml
<function name="OnAppCapabilityUpdated" functionID="OnAppCapabilityUpdatedID" messagetype="notification" since="7.1">
    <description>A notification to inform SDL Core that a specific app capability has changed.</description>
    <param name="appCapability" type="AppCapability" mandatory="true">
        <description>The app capability that has been updated</description>
    </param>
</function>

<struct name="AppCapability" since="7.1">
    <param name="appCapabilityType" type="AppCapabilityType" mandatory="true">
        <description>Used as a descriptor of what data to expect in this struct. The corresponding param to this enum should be included and the only other param included.</description>
    </param>
    <param name="videoStreamingCapability" type="VideoStreamingCapability" mandatory="false">
        <description>Describes supported capabilities for video streaming </description>
    </param>
</struct>

<enum name="AppCapabilityType" since="7.1">
    <description>Enumerations of all available app capability types</description>
    <element name="VIDEO_STREAMING"/>
</enum>
```
