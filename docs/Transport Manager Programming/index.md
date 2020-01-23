# Transport Programming Guide

This guide will explain how transports work in SDL Core. We will highlight the responsibilities of each interface as well as look at some examples of transports already implemented in SDL Core. First let's take a look at Figure 1, a diagram showing the hierarchy of the main transport components and then we will work our way down the diagram describing each component.

|||
Figure 1: Transport Overview
![TAStructure](./assets/tastructure.png)
|||

## Transport Manager

The Transport Manager is responsible for routing commands and messages between the transport adapters and other major components in core. A Transport Manager can contain any number of Transport Adapters, each of which is responsible for handling communication via one type of transport, such as TCP or Bluetooth. The Transport Manager also contains data necessary to handle its responsibilities, such as a mapping of each device to the Transport Adapter it uses to communicate. Other components within Core are also able to register a Transport Manager Listener with the manager, which will receive events from the Transport Manager. The default Transport Manager follows the singleton pattern, but this is not necessary if you would like to use a custom solution.

#### Transport Manager Inheritance Structure

|||
Figure 2: Transport Manager UML Diagram
![TM](./assets/tm.png)
|||

!!! NOTE
Implementation (*Impl) classes just represent concretions and may not actually be named the same in the SDL core project.

UML Refresher

* Aggregation: Solid line with open diamond
* Composition: Solid line with filled diamond
* Inheritance: Dotted line with open arrow
* Dependency: Dotted line with two prong arrow
!!!

## Transport Adapter

Each Transport Adapter is responsible for one specific type of connection, such as TCP or Bluetooth. Similar to Transport Managers, other components in Core are able to register a Transport Adapter Listener with a Transport Adapter to later receive events from the Adapter such as `OnDeviceAdded`. The Transport Adapter will contain the code to connect and disconnect devices, as well as send and receive data. Depending on the transport type, a transport adapter may implement sub-components, called workers, such as a Device Scanner, a Client Connection Listener, or a Server Connection Factory. Currently, Transport Adapters are registered with the Transport Manager within the [TransportManagerDefault::Init](https://github.com/smartdevicelink/sdl_core/blob/develop/src/components/transport_manager/src/transport_manager_default.cc#L62) method; you can add code here to include your custom Transport Adapter. Depending on your implementation, most of the functionality of the Transport Adapter will likely live in the workers. Two big functions that will for sure need to be implemented in a Transport Adapter are Store() and Restore() which are used to save and resume the state of the Adapter. In the case of the TCP Transport Adapter, the Store() function will save a list of devices' names and addresses, along with the applications each device was running and their corresponding port number. When resuming, Restore() will reconnect to the devices saved in the last state and resumes communication with the the applications on each device.

```
// tcp_transport_adapter.cc
// Code has been heavily simplified and whitespace has been added for readability.

void TcpTransportAdapter::Store() const {
  Json::Value devices_dictionary;

  for (Device* tcp_device : GetDeviceList()) {
    if (!tcp_device) { continue; } // device could have been disconnected

    Json::Value device_dictionary;
    device_dictionary["name"] = tcp_device->name();
    device_dictionary["address"] = tcp_device->in_addr();

    Json::Value applications_dictionary;
    for (ApplicationHandle app_handle : tcp_device->GetApplicationList()) {
      if (FindEstablishedConnection(tcp_device->unique_device_id(),
                                    app_handle)) {
        int port = tcp_device->GetApplicationPort(app_handle);
        if (port != -1) {  // don't want to store incoming applications
          applications_dictionary.append(std::string(port));
        }
      }
    }

    if (!applications_dictionary.empty()) {
      device_dictionary["applications"] = applications_dictionary;
      devices_dictionary.append(device_dictionary);
    }
  }

  Json::Value& dict = last_state().get_dictionary();
  dict["TransportManager"]["TcpAdapter"]["devices"] = devices_dictionary;
}
```

#### Transport Adapter Inheritance Structure

|||
Figure 3: Transport Adapter UML Diagram
![TA](./assets/ta.png)
|||

!!! NOTE
Implementation (*Impl) classes only represent concretions and may not actually be named the same in the SDL core project.

UML Refresher

* Aggregation: Solid line with open diamond
* Composition: Solid line with filled diamond
* Inheritance: Dotted line with open arrow
* Dependency: Dotted line with two prong arrow
!!!

### Transport Adapter Workers

The Client Connection Listener implements receiving a connection that is originated by a device. This will typically wait for connection from a device, then establish that connection, finally alerting the Transport Manager via the Transport Manager Listener of the newly connected device and app IDs. The TCP transport adapter has a good example implementation of a Client Connection Listener.

The Server Connection Factory implements a connection that is originated from Core. For example, Core reaches out to a predefined web address to start a cloud websocket application. This type of communication requires that the Transport Adapter knows of the device and application in advance. When this connection is created, the Transport Adapter will alert the Transport Manager of the new devices and applications in a similar fashion to other workers. USB and Bluetooth are additional examples that implement this sub-component.

The Device Scanner is responsible for scanning for new devices to connect with. When a device is found, this worker is responsible for alerting the Transport Adapter, as well as alerting the Transport Manager via the Transport Adapter Listener. Next, the Transport Manager will instruct the Transport Adapter to connect with the devices. The Bluetooth Transport Adapter has a great example implementation of the Device Scanner that search for bluetooth services advertising the SDL bluetooth UUID.

Depending on what your type of transport is, whether Core will be the server or the client, you will likely implement either the Client Connection Listener or the Server Connection Factory.

#### Client Connection Listener

Using the TCP Transport Adapter as an example for a client connection listener implementation, let's take a look at `Init()`.

```
// tcp_client_listener.cc
// Code has been simplified and whitespace has been added for readability.
// Thank you to Sho Amano for the helpful comments.

TransportAdapter::Error TcpClientListener::Init() {
  thread_stop_requested_ = false;

  if (!IsListeningOnSpecificInterface()) {
    // Network interface is not specified. We will listen on all interfaces
    // using INADDR_ANY. If socket creation fails, we will treat it an error.
    socket_ = CreateIPv4ServerSocket(port_);
    if (-1 == socket_) {
      LOG("Failed to create TCP socket");
      return TransportAdapter::FAIL;
    }
  } else {
    // Network interface is specified and we will listen only on the interface.
    // In this case, the server socket will be created once
    // NetworkInterfaceListener notifies the interface's IP address.
    LOG("TCP server socket will listen on " << designated_interface_
                     << " once it has an IPv4 address.");
  }

  if (!interface_listener_->Init()) {
    if (socket_ >= 0) {
      close(socket_);
      socket_ = -1;
    }
    return TransportAdapter::FAIL;
  }

  initialized_ = true;
  return TransportAdapter::OK;
}
```

The Init() function is called one time to prepare the Transport Adapter for its work. If the initialization work within this function succeeds, `initialized_` should be set to true. In the case of the TCP Client Listener, the Init() method will create the socket and initialize the interface listener. It is worth noting that the interface listener is specific to the TCP Client Listener and contains the lower level code to accept TCP connections such as the socket() and bind() syscalls. A similar component is not necessary. The opposite of Init() is the Terminate() method which handles shutting down the transport adapter. On the TCP Client Listener, this involves destroying the socket and de-initializing the interface listener.

```
// tcp_client_listener.cc
// Code has been simplified and whitespace has been added for readability.

TransportAdapter::Error TcpClientListener::StartListening() {
  if (started_) {
    LOG("TransportAdapter::BAD_STATE. Listener has already been started");
    return TransportAdapter::BAD_STATE;
  }

  if (!interface_listener_->Start()) {
    return TransportAdapter::FAIL;
  }

  if (!IsListeningOnSpecificInterface()) {
    TransportAdapter::Error ret = StartListeningThread();
    if (TransportAdapter::OK != ret) {
      LOG("Tcp client listener thread start failed");
      interface_listener_->Stop();
      return ret;
    }
  }

  started_ = true;
  LOG("Tcp client listener has started successfully");
  return TransportAdapter::OK;
}
```

The next set of functions to implement are StartListening() and ResumeListening(), which are fairly similar, both setting `started_` to true when they are ready to send and receive data. In the case of the TCP Client Listener, ResumeListening initializes the interface listener, and starts the listening thread. StartListening follows a very similar pattern of behavior but calls Start() on the interface listener instead of Init().

```
// tcp_client_listener.cc
// Code has been simplified and whitespace has been added for readability.

TransportAdapter::Error TcpClientListener::StopListening() {
  if (!started_) {
    LOG("TcpClientListener is not running now");
    return TransportAdapter::BAD_STATE;
  }

  interface_listener_->Stop();

  StopListeningThread();

  started_ = false;
  LOG("Tcp client listener was stopped successfully");
  return TransportAdapter::OK;
}
```

The StopListening() and SuspendListening() functions do about the opposite, both stop the TCP Client Listener delegate thread and then set `started_` to false. The difference between the two functions on the TCP Client Listener is that StopListening() will also stop the Platform Specific Network Interface Listener's delegate thread. When SuspendListening() is called, SDL will not be able to create new connections, but existing connections are still able to communicate data. StopListening() will also kill communication with existing connections.

#### Server Connection Factory

The Server Connection Factory has the method CreateConnection() which, provided with a device UID and application handle, creates a connection to the application, and then should call ConnectionCreated() on the Transport Adapter.

```
// bluetooth_connection_factory.cc
// Code has been simplified and whitespace has been added for readability.

TransportAdapter::Error BluetoothConnectionFactory::CreateConnection(
    const DeviceUID& device_uid, const ApplicationHandle& app_handle) {
  auto connection = std::make_shared<BluetoothSocketConnection>(
          device_uid, app_handle, controller_);
  controller_->ConnectionCreated(connection, device_uid, app_handle);

  TransportAdapter::Error error = connection->Start();
  if (TransportAdapter::OK != error) {
    LOG("Bluetooth connection::Start() failed with error: "
            << error);
  }

  return error;
}
```

#### Device Scanner

The TCP Transport Adapter does not use a device scanner because it waits for incoming connections. We will use the Bluetooth Transport Adapter’s Device Scanner as an example here.

The Init() function is called once and is responsible for preparing for the life-cycle of your device scanner. Here, the bluetooth device scanner will start the device scanner worker thread. This worker thread will either scan for devices repeatedly or only when requested via a conditional variable. This behavior is determined by the second and third parameters to the constructor, a boolean `auto_repeat_search` and an integer `auto_repeat_pause_sec`. If `auto_repeat_search` is set to false, the device scanner will only scan when instructed to, otherwise it will scan every `auto_repeat_pause_sec` seconds.

```
// bluetooth_device_scanner.cc
// Code has been simplified and whitespace has been added for readability.

void BluetoothDeviceScanner::Terminate() {
  shutdown_requested_ = true;

  if (thread_) {
    {
      sync_primitives::AutoLock auto_lock(device_scan_requested_lock_);
      device_scan_requested_ = false;
      device_scan_requested_cv_.NotifyOne();
    }

    LOG("Waiting for bluetooth device scanner thread termination");
    thread_->stop();
    LOG("Bluetooth device scanner thread stopped");
  }
}
```

The Terminate() function is called when Core begins shutting down. It will be responsible for telling the worker thread to finish up.
In the case of the bluetooth device scanner, the destructor will join the thread that was started in Init() and cleanup after it.

```
// bluetooth_device_scanner.cc
// Code has been simplified and whitespace has been added for readability.

TransportAdapter::Error BluetoothDeviceScanner::Scan() {
  if (!IsInitialised() || shutdown_requested_) {
    LOG("BAD_STATE");
    return TransportAdapter::BAD_STATE;
  }

  if (auto_repeat_pause_sec_ == 0) {
    return TransportAdapter::OK;
  }

  sync_primitives::AutoLock auto_lock(device_scan_requested_lock_);
  if (!device_scan_requested_) {
    LOG("Requesting device Scan");
    device_scan_requested_ = true;
    device_scan_requested_cv_.NotifyOne();
  } else {
    return TransportAdapter::BAD_STATE;
  }

  return TransportAdapter::OK;
}
```

```
// bluetooth_device_scanner.cc
// Code has been simplified and whitespace has been added for readability.

void BluetoothDeviceScanner::UpdateTotalDeviceList() {
  std::vector<Device*> devices;
  devices.insert(devices.end(),
    paired_devices_with_sdl_.begin(), paired_devices_with_sdl_.end());
  devices.insert(devices.end(),
    found_devices_with_sdl_.begin(), found_devices_with_sdl_.end());

  controller_->SearchDeviceDone(devices);
}
```

The Scan() function returns an error code, not the actual results of the scan. When the scanning is complete, all devices (existing and newly found) will be passed to the Transport Adapter via the function SearchDeviceDone(). In the Bluetooth device scanner, the Scan() function will signal to the scanning thread that a device scan was requested.

## Operation Examples

|||
New Device Search
![New Device Search](./assets/newDeviceSearch.png)
|||

|||
New Device Connection
![New Connection](./assets/newDeviceConnection.png)
|||

|||
Connection Close Command
![Connection Close](./assets/connectionCloseCommand.png)
|||
