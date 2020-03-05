# SDL Core FAQ

Here are a few of the most common questions new developers have around the SDL Core project. 

## What OS should I use to get started?
Currently the SDL Core repo is built for Ubuntu 18.04 as our default environment.

## I'm getting a lot of compilation errors, how do I get past them?
The most common errors come from dependencies issues. Ensure your system has all the required packages to compile the project. Try running the commands in the [dependencies section](../getting-started/install-and-run/#dependencies) of the Getting Started guide.

## Can I use SDL on Android OS?

There is no official port at the moment, so individual investigation will need to be done. Even though SDL is designed to work on most Linux systems, modifications might need to be made to the project to get it to work with your setup.

## Why are my RPC requests being DISALLOWED by SDL?

The `DISALLOWED` [result code](https://smartdevicelink.com/en/guides/sdl-overview-guides/rpc-spec/#result) is related to the RPC not being authorized in SDL Core's local policy table. Policy permissions for an app are added either in the [preloaded policy table](https://github.com/smartdevicelink/sdl_core/blob/master/src/appMain/sdl_preloaded_pt.json) or through a [policy table update](https://smartdevicelink.com/en/guides/sdl-overview-guides/policies/overview/#policy-table-updates).

The [Policies Overview](https://smartdevicelink.com/en/guides/sdl-overview-guides/policies/overview/) page provides general information about policies - explaining what they are used for, how the policy table gets updated, and how these updates are triggered. [Policy Table Fields](https://smartdevicelink.com/en/guides/sdl-overview-guides/policies/policy-fields/) and [App Policies](https://smartdevicelink.com/en/guides/sdl-overview-guides/policies/app-policies/) go into more detail about the policy table structure and how to correctly add policy permissions for an application.

## Changes I made to my preloaded policy table aren't reflected in SDL Core; what should I do?

If you are not running SDL Core for the first time, SDL Core will use the existing policy table database(`policy.sqlite`) which is stored in the build folder under `bin/storage/`. To make SDL Core parse the preloaded policy table again you have to delete the existing policy table database. In the bin folder run:

```shell
    rm storage/policy.sqlite
```

## Can I build SDL with/without certain features (such as logging or build tests)?

You can enable/disable certain features by modifying the [CMakeLists.txt](https://github.com/smartdevicelink/sdl_core/blob/master/CMakeLists.txt) file. The [CMake Build Configuration section](../getting-started/install-and-run/#cmake-build-configuration) contains a list of features which can be included/excluded for a build.

## What options can I modify in SDL without having to rebuild?

The [SmartDeviceLink.ini](https://github.com/smartdevicelink/sdl_core/blob/master/src/appMain/smartDeviceLink.ini) file located in your `build/src/appMain` directory is where runtime options can be configured for your instance of SDL Core. The [INI Configuration](../getting-started/ini-configuration) page has more information about individual runtime options.

## I'm experiencing choppy audio through Bluetooth; what should I do?
The default SDL Core repo actually performs an SDP on loop. Because SDP queries are a resource intensive operation it can cause the audio coming from the phone to become very choppy. This can be fixed by doing the following:

First, navigate to [this line](https://github.com/smartdevicelink/sdl_core/blob/master/src/components/transport_manager/src/bluetooth/bluetooth_transport_adapter.cc#L61) that reads:

```c++
    : TransportAdapterImpl(new BluetoothDeviceScanner(this, true, 0),
```

Change it to:

```c++
    : TransportAdapterImpl(new BluetoothDeviceScanner(this, false, 0),
```
That will cause the SDP queries to not be performed by default. This means you will need to create a way to perform SDP queries using an event trigger. So in the HMI implementation you will need to tie an event (button press or voice command) to sending the following RPC message to the Core service:

```javascript
    return ({
        'jsonrpc': '2.0',
        'method': 'BasicCommunication.OnStartDeviceDiscovery'
    })
```
## What is the integration time of SDL in an infotainment system?
Timing is dependent on the OEM or Supplier implementing SDL, and also dependent on factors such as OS, hardware, etc.
