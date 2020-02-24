# SDL Core FAQ

Here are a few of the most common questions new developers have around the SDL Core project. 

## What OS should I use to get started?
Currently the SDL Core repo is built for Ubuntu 18.04 as our default environment.

## I'm getting a lot of compilation errors, how do I get past them?
The most common errors come from dependancies issues. Ensure your system has all the required packages to compile the project. Try running the following commands:

```bash
sudo apt-get update
sudo apt-get install git cmake build-essential sqlite3 libsqlite3-dev libssl1.0-dev libssl1.0.0 libusb-1.0-0-dev libudev-dev libgtest-dev libbluetooth3 libbluetooth-dev bluez-tools libpulse-dev python3-pip python3-setuptools
```

## I'm experiencing choppy audio through bluetooth, what should I do?
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
