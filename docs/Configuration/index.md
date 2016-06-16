There are several different types of configurations for SDL that you'll have to understand in order for SDL to work properly and with the features you want on your embedded platform.

### cmake
You'll use the cmake configuration to set up SDL before you compile, and enable or disable features like logging. The cmake file is located at [sdl_core/CMakeLists.txt](https://github.com/smartdevicelink/sdl_core/blob/master/CMakeLists.txt).

### smartDeviceLink.ini
The ini file located at `build/src/appMain/smartDeviceLink.ini` after you compile and install SDL is your main configuration file for runtime configurations.

### sdl_preloaded_pt.json
The policy table located in `build/src/appMain/sdl_preloaded_pt.json` after you compile and install SDL is the default policy table which provides the permissions and default configurations for SDL on its first run before it receives an update from a policy server.

!!! note

If you don't have a policy server and want to experiment with changes in the policy table, you can either edit the policy database directly with sqlite3 or edit the sdl_preloaded_pt.json, remove the build/src/appMain/storage folder, and restart SDL to load the new configuration.

!!!