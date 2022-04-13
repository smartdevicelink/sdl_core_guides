# Migrating SDL Core 8.0 to 8.1

## API changes

The 8.1 release had a few changes to the HMI API that will require updates to your SDL Core integration in your head unit.

### Removal of UI.SetDisplayLayout

With the release of SDL Core 8.1, the `UI.SetDisplayLayout` RPC has been removed from the HMI API.

```xml
...
-  <function name="SetDisplayLayout" messagetype="request">
-    <description>This RPC is deprecated. Use Show RPC to change layout.</description>
-    <param name="displayLayout" type="String" maxlength="500" mandatory="true">
-      <description>
-        Predefined or dynamically created screen layout.
-        Currently only predefined screen layouts are defined.
-      </description>
-    </param>
-    <param name="appID" type="Integer" mandatory="true">
-      <description>ID of application related to this RPC.</description>
-    </param>
-    <param name="dayColorScheme" type="Common.TemplateColorScheme" mandatory="false"></param>
-    <param name="nightColorScheme" type="Common.TemplateColorScheme" mandatory="false"></param>
-  </function>

-  <function name="SetDisplayLayout" messagetype="response">
-    <description>This RPC is deprecated. Use Show RPC to change layout.</description>
-    <param name="displayCapabilities" type="Common.DisplayCapabilities" mandatory="false">
-      <description>See DisplayCapabilities</description>
-    </param>
-    <param name="buttonCapabilities" type="Common.ButtonCapabilities" minsize="1" maxsize="100" array="true" mandatory="false">
-      <description>See ButtonCapabilities</description >
-    </param>
-    <param name="softButtonCapabilities" type="Common.SoftButtonCapabilities" minsize="1" maxsize="100" array="true" mandatory="false">
-      <description>If returned, the platform supports on-screen SoftButtons; see SoftButtonCapabilities.</description >
-    </param>
-    <param name="presetBankCapabilities" type="Common.PresetBankCapabilities" mandatory="false">
-      <description>If returned, the platform supports custom on-screen Presets; see PresetBankCapabilities.</description >
-    </param>
-  </function>
...
```

When an app sends a `SetDisplayLayout` request, SDL now transforms it into a `UI.Show` request (with the `templateConfiguration` parameter set based on the parameters defined in the `SetDisplayLayout` request) and forwards it to the HMI. The `UI.SetDisplayLayout` implementation was also removed from the [SDL HMI](https://github.com/smartdevicelink/sdl_hmi/pull/664) and [Generic HMI](https://github.com/smartdevicelink/generic_hmi/pull/499). However, developers may decide to keep their implementation to support older versions of SDL Core.

### Removal of duplicate parameter from BasicCommunication.OnPutFile

The duplicate parameter `FileName` was removed from the `BasicCommunication.OnPutFile` RPC in the HMI API

```xml
<function name="OnPutFile" messagetype="notification" >
      <description>
        Notification that is sent to HMI when a mobile application uploads a file
      </description>
      ...
-     <param name="FileName" type="String" maxlength="255" mandatory="true">
-       <description>File reference name.</description>
-     </param>

      <param name="syncFileName" type="String" maxlength="255" mandatory="true">
        <description>File reference name.</description>
      </param>
      <param name="fileType" type="Common.FileType" mandatory="true">
          <description>Selected file type.</description>
      </param>
...
```

The parameter was unused. SDL Core uses `syncFileName` in the notification sent to the HMI.

## Core behavior changes

### Reject PROPRIETARY/HTTP SystemRequests when PTU is not in progress

With the release of 8.1, SDL Core will now reject incoming `PROPRIETARY`/`HTTP` SystemRequests when a policy table update (PTU) is not in progress and if an application not selected for the PTU sends the request.

This was identified as a security flaw since it would allow any application to trigger a PTU. For more information please see proposal [0337](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0337-reject-proprietary-http-systemrequests-when-ptu-not-in-progress.md#motivation).


