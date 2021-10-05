# Migrating SDL Core 7.1 to 8.0

## Environment Updates

### Ubuntu Versions

SDL Core 8.0.0 no longer supports Ubuntu 16. Supported versions of SDL Core are Ubuntu 18.04 and Ubuntu 20.04.

### SSL versions

SDL Core 8.0.0 dropped support for libssl1.0. Developers should install libssl-dev instead of libssl1.0-dev

## Updates to CMAKE Build Configuration

### Removed Flag `ENABLE_HMI_PTU_DECRYPTION`

`ENABLE_HMI_PTU_DECRYPTION` was removed from the build configuration. Behaviors defined by ON/OFF options are now both supported without the need for this build flag.

### Boost Logger

The default logger is still using LOG4CXX but the option is now available to use Boost for the logger. When porting SDL Core to different linux environments, the LOG4CXX logger was known to cause dependency issues. Boost is offered as an alternative logger in hopes to make porting SDL Core to different environments easier.

```
set(LOGGER_NAME "LOG4CXX" CACHE STRING "Logging library to use (BOOST, LOG4CXX)")
```

## Updates to Configuration File smartDeviceLink.ini

### DefaultTimeoutCompensation

This parameter was added to the smartDeviceLink.ini configuration to help compensate for timing issues that could occur during `BasicCommunication.OnResetTimeout`. This value is added to the DefaultTimeout parameter when calculating the RPC request timeout.

```
; Extra time to compensate default timeout due to external delays
DefaultTimeoutCompensation = 1000
```

### Icons Storage Folder

The default parameter `AppIconsFolder` was updated to use a directory named “icons”. This value used to be “storage”.

```
; Specify a dedicated folder, as old files in this folder can be automatically removed
AppIconsFolder = icons
```

## HMI API Updates

## Buttons.SubscribeButton

`Buttons.OnButtonSubscription` notification was replaced by `Buttons.SubscribeButton` Request and Response.

```xml
<function name="SubscribeButton" messagetype="request">
        <description>
            Subscribes to buttons.            
        </description>
		
	    <param name="appID" type="Integer" mandatory="true">
			<description>The ID of the application requesting this button usubscription. </description>
        </param>
		
        <param name="buttonName" type="ButtonName" mandatory="true">
            <description>Name of the button to subscribe.</description>
        </param>
</function>

<function name="SubscribeButton" messagetype="response"> </function>
```

## Buttons.UnsubscribeButton


`Buttons.UnsubscribeButton` request and response were added to allow SDL Core to request the HMI unsubscribes an application from a specific button.

```
<function name="UnsubscribeButton" messagetype="request">
        <description>
            Unsubscribes from buttons.            
        </description>
        
        <param name="appID" type="Integer" mandatory="true">
            <description>The ID of the application requesting this button unsubscription. </description>
        </param>
        
        <param name="buttonName" type="ButtonName" mandatory="true">
            <description>Name of the button to unsubscribe.</description>
        </param>
    </function>

<function name="UnsubscribeButton" messagetype="response"></function>

```

## Restructuring OnResetTimeout 

`UI.OnResetTimeout` and `TTS.OnResetTimeout` were removed in place of using a broader rpc, `BasicCommunication.OnResetTimeout`.

This updated `OnResetTimeout` RPC can be used across all interfaces for all request functions.

The parameters in the notification have also changed.

The parameter `requestID` is used instead of appID to identify which specific request should have its timeout extended.

The parameter `methodName` should include the interface name and the rpc. For example: `”TTS.Speak”`.

The parameter `resetPeriod` allows the HMI to specify how long Core should delay the application request’s timeout.


```
<interface name="BasicCommunication">
...
<function name="OnResetTimeout" messagetype="notification" since="X.Y">
    <description>
		HMI must send this notification to SDL for method instance for which timeout needs to be reset
    </description>	
    <param name="requestID" type="Integer" mandatory="true">	
		<description>
			Id between HMI and SDL which SDL used to send the request for method in question, for which timeout needs to be reset.
		</description>
    </param>
    <param name="methodName" type="String" mandatory="true">
		<description>
			Name of the function for which timeout needs to be reset
		</description>
    </param>
    <param name="resetPeriod" type="Integer" minvalue="0" maxvalue="1000000" mandatory="false">
		<description>
			Timeout period in milliseconds, for the method for which timeout needs to be reset.
			If omitted, timeout would be reset by defaultTimeout specified in smartDeviceLink.ini
		</description>
    </param>
</function>
…
</interface>

```
