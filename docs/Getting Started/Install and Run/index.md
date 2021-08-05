# Installation
A quick guide to installing, configuring, and running an instance of SDL Core on a Linux OS (default environment is Ubuntu 20.04 LTS).

## Dependencies
The dependencies for SDL Core vary based on the configuration. You can change SDL Core's build configuration in the top level CMakeLists.txt. We have defaulted this file to a configuration which we believe is common for people who are interested in getting up and running quickly, generally on a Linux VM.

The default dependencies for SDL Core can be installed with the following command:

```bash
sudo apt-get install git cmake build-essential sqlite3 libsqlite3-dev libssl-dev libssl1.1 libusb-1.0-0-dev libudev-dev libgtest-dev libbluetooth3 libbluetooth-dev bluez-tools libpulse-dev python3-pip python3-setuptools python
```

### Clone SDL Core and Submodules

To get the source code of SDL Core, clone the [git repository](https://github.com/smartdevicelink/sdl_core) like so:

```bash
git clone https://github.com/smartdevicelink/sdl_core
```

Before building for the first time, there are a few commands that need to be run in the source folder to initialize the project:

```bash
cd sdl_core
git submodule init
git submodule update
``` 

### CMake Build Configuration
CMake is used to configure your SDL Core build before you compile the project, this is where you can enable or disable certain features such as logging. The latest list of CMake configuration options can be found in the root CMake file of the project, located at [sdl_core/CMakeLists.txt](https://github.com/smartdevicelink/sdl_core/blob/master/CMakeLists.txt). Listed below are the possible configurations for these options, default values are bolded.

#### Transport Options
|Option|Value(s)|Dependencies|Description|
|:-----|:-------|:-----------|:----------|
|BUILD_BT_SUPPORT|**ON**/OFF|BlueZ (packages: libbluetooth3, libbluetooth-dev, bluez-tools)|Enable/Disable bluetooth transport support via BlueZ|
|BUILD_USB_SUPPORT|**ON**/OFF|libusb (packages: libusb-1.0-0-dev, libudev-dev)|Enable/Disable USB transport support via libusb|
|BUILD_CLOUD_APP_SUPPORT|**ON**/OFF|Boost (included in project)|Enable/Disable SDL Cloud application support via boost websocket transport|

#### Feature Support Options
|Option|Value(s)|Dependencies|Description|
|:-----|:-------|:-----------|:----------|
|EXTENDED_MEDIA_MODE|ON/**OFF**|GStreamer, PulseAudio (packages: libpulse-dev)|Enable/Disable audio pass thru via PulseAudio mic recording. When this option is disabled, Core will emulate audio pass thru by sending a looped audio file.|
|ENABLE_SECURITY|**ON**/OFF|OpenSSL (packages: libssl-dev)|Enable/Disable support for secured SDL protocol services|
|EXTENDED_POLICY|HTTP|N/A|HTTP (simplified) Policy flow. `OnSystemRequest` is sent with HTTP RequestType to initiate a policy table update. The HMI is not involved in the PTU process in this mode, meaning that policy table encryption is not supported.|
|EXTENDED_POLICY|**PROPRIETARY**|N/A|Default Policy flow, PROPRIETARY RequestType. Simplified policy feature set (no user consent, encryption/decryption only available via HMI)|
|EXTENDED_POLICY|EXTERNAL_PROPRIETARY|packages: python-pip, python-dev (If using the included sample policy manager, which is automatically started by `core.sh` by default)|Full Policy flow, PROPRIETARY RequestType. Full-featured policies, along with support for handling encryption/decryption via external application|

#### Development/Debug Options
|Option|Value(s)|Dependencies|Description|
|:-----|:-------|:-----------|:----------|
|ENABLE_LOG|**ON**/OFF|log4cxx (included in project)/boost logger|Enable/Disable logging tool. Logs are stored in `<sdl_build_dir>/bin/SmartDeviceLinkCore.log`.|
|LOGGER_NAME|**LOG4CXX**|log4cxx (included in project)|Build with the apache log4cxx logger. Log properties can be configured in `<sdl_build_dir>/bin/log4cxx.properties`|
|LOGGER_NAME|BOOST|boost logger|Build with the boost logger library. Log properties can be configured in `<sdl_build_dir>/bin/boostlogconfig.ini`|
|BUILD_TESTS|ON/**OFF**|GTest (packages: libgtest-dev)|Build unit tests (run with `make test`)|
|USE_COTIRE|**ON**/OFF|N/A|Option to use [cotire](https://github.com/sakra/cotire) to speed up the build process when BUILD_TESTS is ON.|
|USE_GOLD_LD|**ON**/OFF|N/A|Option to use gold linker in place of gnu ld to speed up the build process.|
|ENABLE_SANITIZE|ON/**OFF**|N/A|Option to compile with `-fsanitize=address` for fast memory error detection|

## Building

After installing the appropriate dependencies for your build configuration, you can run `cmake` with your chosen options. 

Begin by creating a build folder **outside** of SDL Core source folder, for example:

```bash
cd ..
mkdir sdl_build
cd sdl_build
```

From the build folder you created, run `cmake {path_to_sdl_core_source_folder}`  with any flags that you want to change in the format of `-D<option-name>=<value>`, for example:

```bash
cmake ../sdl_core
```

From there, you can build and install the project, run the following commands in your build folder:

```bash
make install-3rd_party
make install
```

For a faster build, you can run the last command with the `-j` flag, which will enable multithreaded building:

```bash
make -j `nproc` install
```

## Start SDL Core
Once SDL Core is compiled and installed, you can start it using the provided start script in the newly created bin folder under your build folder directory

```bash
cd bin/
./start.sh
```

If you get a linking error when running Core, the following command may be needed to resolve it:

```bash
sudo ldconfig
```

In addition, you can run SDL Core as a background process using the provided daemon script. This is useful for controlling the lifecycle of Core when creating automated scripts for your system.

To start SDL Core in the background:
```bash
./core.sh start
```

To restart SDL Core while it is running in the background:
```bash
./core.sh restart
```

To stop SDL Core while it is running in the background:
```bash
./core.sh stop
```

To kill any lingering instances of SDL Core (including those that were not started using the script):
```bash
./core.sh kill
```

!!! NOTE
If Core was built with `EXTENDED_POLICY=EXTERNAL_PROPRIETARY`, the `core.sh` script will automatically start the provided sample policy manager along with Core. To disable this, run the daemon script as such:
```bash
./core.sh <command> false
``` 
!!!

## Example - EXTERNAL_PROPRIETARY build

!!! Note
To perform a completely clean build after previously building SDL Core, delete the existing build folder before running these steps:
```
rm -rf sdl_build
```
!!!

The following steps can be used to build the develop branch of SDL Core from scratch with the `EXTERNAL_PROPRIETARY` policy mode enabled:

### First Time Setup

The following commands only need to be run on the first installation of the project

```
sudo apt-get install git cmake build-essential sqlite3 libsqlite3-dev libssl-dev libssl1.1 libusb-1.0-0-dev libudev-dev libgtest-dev libbluetooth3 libbluetooth-dev bluez-tools libpulse-dev python3-pip python3-setuptools python
git clone https://github.com/smartdevicelink/sdl_core
```

### Configuration

```
cd sdl_core
git checkout develop
git pull
git submodule init
git submodule update
```

### Installation

```
cd ..
mkdir sdl_build
cd sdl_build
cmake ../sdl_core -DEXTENDED_POLICY=EXTERNAL_PROPRIETARY
make install-3rd_party
make -j3 install
```
