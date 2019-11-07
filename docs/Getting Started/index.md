
# Getting Started
A quick guide to installing, configuring, and running an instance of the SDL Core on a linux OS.

First, clone [SDL Core](https://github.com/smartdevicelink/sdl_core), then create a folder for your build outside of the source folder.

## Dependencies
The dependencies for SDL Core vary based on the configuration. You can change SDL Core's build configuration in the top level CMakeLists.txt. We have defaulted this file to a configuration which we believe is common for people who are interested in getting up and running quickly, generally on a Linux VM.

The default dependencies for SDL Core can be installed with the following command:
```
sudo apt-get install git cmake build-essential sqlite3 libsqlite3-dev libssl1.0-dev libssl-1.0.0 libusb-1.0-0-dev libudev-dev libgtest-dev libbluetooth3 libbluetooth-dev bluez-tools libpulse-dev
```

### CMake Build Configuration
You'll use the CMake configuration to set up SDL before you compile, and enable or disable features like logging. The latest list of CMake configurations can be found in the root CMake file of the project, located at [sdl_core/CMakeLists.txt](https://github.com/smartdevicelink/sdl_core/blob/master/CMakeLists.txt). Listed below are the possible configurations, default values are bolded.

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
|ENABLE_SECURITY|**ON**/OFF|OpenSSL (packages: libssl1.0-dev)|Enable/Disable support for secured SDL protocol services|
|EXTENDED_POLICY|HTTP|N/A|HTTP (simplified) Policy flow. `OnSystemRequest` is sent with HTTP RequestType to initiate a policy table update. The HMI is not involved in the PTU process in this mode, meaning that policy table encryption is not supported.|
|EXTENDED_POLICY|**PROPRIETARY**|N/A|Default Policy flow, PROPRIETARY RequestType. Simplified policy feature set (no user consent, encryption/decryption only available via HMI)|
|EXTENDED_POLICY|EXTERNAL_PROPRIETARY|packages: python-pip, python-dev (If using the included sample policy manager, which is automatically started by `start.sh` by default)|Full Policy flow, PROPRIETARY RequestType. Full-featured policies, along with support for handling encryption/decryption via external application|
|ENABLE_HMI_PTU_DECRYPTION|**ON**/OFF|N/A|Only applies to PROPRIETARY mode. When enabled, the HMI is expected to decrypt the policy table before sending `SDL.OnReceivedPolicyUpdate`.|

#### Development/Debug Options
|Option|Value(s)|Dependencies|Description|
|:-----|:-------|:-----------|:----------|
|ENABLE_LOG|**ON**/OFF|log4cxx (included in project)|Enable/Disable logging tool. Logs are stored in `<sdl_build_dir>/bin/SmartDeviceLinkCore.log` and can be configured by `log4cxx.properties` in the same folder.|
|BUILD_TESTS|ON/**OFF**|GTest (packages: libgtest-dev)|Build unit tests (run with `make test`)|
|USE_COTIRE|**ON**/OFF|N/A|Option to use [cotire](https://github.com/sakra/cotire) to speed up the build process when BUILD_TESTS is ON.|
|USE_GOLD_LD|**ON**/OFF|N/A|Option to use gold linker in place of gnu ld to speed up the build process.|
|ENABLE_SANITIZE|ON/**OFF**|N/A|Option to compile with `-fsanitize=address` for fast memory error detection|

## Building

After installing the appropriate dependencies for your build configuration, you can run cmake with your chosen configuration. 

From the build folder you created, run `cmake {path_to_sdl_core_source_folder}`  with any flags that you want to change in the format of `-D<option-name>=<value>`

From there, you can build the project, run the following commands in your build folder:
```
make
make install
```

## Start SDL Core
Once SDL Core is compiled and installed you can start it from the executable in the bin folder

```
cd bin/
./start.sh
```

If, for some reason you get a linking error when running Core, you might need to run the following command:

```
sudo ldconfig
```
