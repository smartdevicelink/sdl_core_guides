# Transport Progamming Guide

Hello, this guide will explain how transports work in SDL Core. We will highlight the responsibilities of each interface as well as look at some examples of transports already implemented in SDL Core. First let's take a look at Figure 1, a diagram showing the structure of the main Transport components and then we will work our way down the diagram describing each component.

|||
Figure 1
![TAStructure](./assets/tastructure.png)
|||

## Transport Manager

The Transport Manager is responsible for the sending and receiving of data, as well as the connecting and disconnecting of devices. These responsibilities are abstracted away by Transport Adapters. A Transport Manager can contain any number of Transport Adapters, each of which is responsible for handling communication via one type of transport, such as TCP or Bluetooth. The Transport Manager also contains data necessary to handle its responsibilities, such as a mapping of each device to the Transport Adapter it uses to communicate. Other components within Core are also able to register a Transport Manager Listener with the Transport Manager, which will receive events from the Transport Manager. The default Transport Manager follows the singleton pattern, but this is not necessary if you would like to use a custom Transport Manager.

#### Transport Manager Structure

Figure 2 represents the structure of the Transport Manager

|||
Figure 2
![TM](./assets/tm.png)
|||

## Transport Adapter

Each Transport Adapter is responsible for one specific type of transporting data, such as TCP or Bluetooth. The Transport Adapter will contain the code to connect and disconnet devices, as well as transmit data. Many of the existing Transport Adapters also implement workers where relevant, such as a Device Scanner, a Client Connection Listener, or a Server Connection Factory. Currently, Transport Adapters are registered with the Transport Manager within `TransportManagerDefault::Init()` method, you can add code here to also include your custom Transport Adapter. The two main functions that will need to be implemented in a Transport Adapter are Store() and Restore() which are used to save and resume the state of the Adapter. In the case of the TCP Transport Adapter, the Store() function will save a list of devices' names and addresses, along with the applications each device was running and their corresponding port number. When resuming, Restore() will reconnect to the devices saved in the last state and resumes communication with the the applications on each device. Similar to Transport Managers, other components in Core are able to register a Transport Adapter Listener with a Transport Adapter to later receive events from the Adapter such as `OnDeviceAdded`.

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

#### Transport Adapter Structure

Figure 3 represents the structure of a Transport Adapter

|||
Figure 3
![TA](./assets/ta.png)
|||

### Transport Adapter Workers

The Device Scanner is responsible for scanning for new devices to connect with. When a device is found, this worker is responsible for alerting the Transport Adapter, as well as alerting the Transport Manager via the Transport Adapter Listener. Next, the Transport Manager will instruct the Transport Adapter to connect with the devices.
The Client Connection Listener implements receiving a connection that is originated by a device. This will typically wait for connection from a device, then establish that connection, finally alerting the Transport Manager via the Transport Manager Listener of the newly connected device and app IDs.
The Server Connection Factory implements a connection that is originated from Core. For example, Core reaches out to a predefined web address to start a cloud application. This type of communication requires that the Transport Adapter knows of the device and application in advance. When this connection is created, the Transport Adapter will alert the Transport Manager of the new devices and applications in a similar fashion to other workers.

Depending on what your type of transport is, whether Core will be the server or the client, you will likely implement either the Client Connection Listener or the Server Connection Factory.

#### Client Connection Listener

Since the TCP Transport Adapter is running a server that clients will connect to, much of the implementation lives in the Client Connection Listener. The Client Connection Listener has a fair few functions to implement, let’s start with Init(). 

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

The Init() function is called one time to prepare the Transport Adapter for its work. If the initialization work within this function succeeds, `initialized_` should be set to true. In the case of the TCP Client Listener, the Init() method will create the socket and initialize the interface listener. It is worth noting that the interface listener is specific to the TCP Client Listener and contains the lower level code to accept TCP connections such as the socket() and bind() syscalls. A similar component is not necessary. The opposite of Init() is the Terminate() method which handles shutting down the transport adapter. On the TCP Client Listener, this involves destroying the socket and deinitializing the interface listener.

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

The next set of functions to implement are StartListening() and ResumeListening(), which are fairly similar, both setting `started_` to true when they are reaady to send and receive data. In the case of the TCP Client Listener, ResumeListening initializes the interface listener, and starts the listening thread. StartListening follows a very similar pattern of behavior but calls Start() on the interface listener instead of Init().

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

The Server Connection Factory has the method CreateConnection() which, provided with a device UID and application handle, creates a connection to the application, and then should call ConnectionCreated() on the TransportAdapter.

```
// tcp_connection_factory.cc
// Code has been simplified and whitespace has been added for readability.

TransportAdapter::Error TcpConnectionFactory::CreateConnection(
    const DeviceUID& device_uid, const ApplicationHandle& app_handle) {
  LOG("DeviceUID: " << &device_uid << ", ApplicationHandle: " << &app_handle);

  auto connection = TcpServerOriginatedSocketConnection(
            device_uid, app_handle, controller_);
  controller_->ConnectionCreated(connection, device_uid, app_handle);

  const TransportAdapter::Error error = connection->Start();
  if (TransportAdapter::OK != error) {
    LOG("TCP ServerOriginated connection::Start() failed with error: "
            << error);
  }

  return error;
}
```

#### Device Scanner

The TCP Transport Adapter does not use a device scanner because it waits for incoming connections. We will use the Bluetooth Transport Adapter’s Device Scanner as an example here.

The Init() function is called once and is responsible for preparing for the lifecycle of your device scanner. Here, the bluetooth device scanner will start the device scanner worker thread. This worker thread will either scan for devices repeatedly or only when requested via a conditional variable.

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
