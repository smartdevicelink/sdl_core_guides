#SDL Core Video Stream Setup

## Initial Configuration
###Install Packages


```

sudo apt-get install git cmake build-essential libavahi-client-dev libsqlite3-dev chromium-browser libssl-dev libudev-dev libgtest-dev libbluetooth3 libbluetooth-dev bluez-tools gstreamer1.0* libpulse-dev

```
```

sudo apt-get update
sudo apt-get upgrade
sudo ldconfig
```

##Clone the SDL Core Repository

Clone the SDL Core [repository](https://github.com/smartdevicelink/sdl_core)

```
git clone https://github.com/smartdevicelink/sdl_core.git
```

CD into sdl_core/ and checkout the [master branch](https://github.com/smartdevicelink/sdl_core/tree/master)

```
git checkout -b master origin/master
```
##Clone the SDL HMI Repository

Clone the Web HMI [repository](https://github.com/smartdevicelink/sdl_hmi)

```
git clone https://github.com/smartdevicelink/sdl_hmi.git
```

CD into sdl_hmi/ and checkout the [master branch](https://github.com/smartdevicelink/sdl_hmi/tree/master)

```
git checkout -b release/4.0.0 origin/master
```
##Setup the Build Environment

Create build folder outside of sdl_core/ directory

```
mkdir build
cd build
```

Run CMAKE and install application
```
cmake ../sdl_core
make
make install
```

Copy keys from bin/ to src/appMain

```
cp bin/myKey.pem src/appMain
cp bin/myCert.pem src/appMain
```
##Pipe Stream Setup

###GSTREAMER Setup

First, we must determine what gstreamer command works in your environment

Start by finding a raw h.264 file ([Example](https://support.apple.com/library/APPLE/APPLECARE_ALLGEOS/HT1425/sample_iPod.m4v.zip)) and determine which of the these gstreamer commands sucessfully plays the example video

- Ubuntu 14.04+
```
gst-launch-1.0 filesrc location=/path/to/h264/file ! decodebin ! videoconvert ! xvimagesink sync=false
```
or
```
gst-launch-1.0 filesrc location=/path/to/h264/file ! decodebin ! videoconvert ! ximagesink sync=false
```

If you're using tcp, you can connect the stream directly with your phones ip address using
```
gst-launch-1.0 tcpclientsrc host=<Device IP Address> port=3000 ! decodebin ! videoconvert ! ximagesink sync=false
```

- Audio Stream Pipe (Raw PCM)
```
gst-launch-1.0 filesrc location=$SDL_BUILD_PATH/src/appmain/storage/audio_stream_pipe ! audio/x-raw,format=S32BE,rate=8000,channels=1 ! pulsesink
```
###Make configuration changes to smartDeviceLink.ini
In the build folder directory, open src/appMain/smartDeviceLink.ini in a text editor and the make the following change

Under [MEDIA MANAGER] comment out the socket stream options and remove semicolon from the pipe stream options
```
;VideoStreamConsumer = socket
;AudioStreamConsumer = socket
;VideoStreamConsumer = file
;AudioStreamConsumer = file
VideoStreamConsumer = pipe
AudioStreamConsumer = pipe
```

###Prerolling Pipeline

After you start SDL Core, cd into the src/appMain/storage directory and there should be a file named "video_stream_pipe". Use the gst-launch command that worked for your environment and set file source to the video_stream_pipe file. You should see “setting pipeline to PAUSED” and “Pipeline is PREROLLING”.

```
gst-launch-1.0 filesrc location=$SDL_BUILD_PATH/src/appmain/storage/video_stream_pipe ! decodebin ! videoconvert ! xvimagesink sync=false
```

#Start SDL Core
In the build folder

```
cd src/appMain
./smartDeviceLinkCore
```
NOTE: If you want to use a USB connection, you must run
```
sudo ./smartDeviceLinkCore
```

###Start the web HMI

CD into the HMI repository and run
```
chromium-browser index.html
```
###Start Video Stream
To Do: Provide public mobile application that supports video streaming.

Mobile application settings (Wifi/TCP connection):

  * Deselect Heartbeat.
  * Select MobileNavi (video source).
  * Select H264 video format.
  * Select wifi as the connection type.
  * Input the Virtual Machine's IP Address and port 12345.
  * Press the Ok button in the App to start the connection.
  * In the web HMI, click the italic "i" and select your app.
  * In the app, select "start service" to request permission to stream video.
  * The HMI will prompt you to give permission to the app to stream video. Click "Ok"
  * In the app, press "Start File Streaming". Depending on your video playback configuration, the video will begin playing in the web browser, or a gstreamer window open and begin playing the video.

Mobile application settings (USB connection):

  * After core is started, connect a phone to your machine with a usb cable.
  * The phone will prompt which app you want to run, select your app.
  * Start the app on your phone.
  * Deselect Heartbeat.
  * Select MobileNavi (video source).
  * Select H264 video format.
  * Select usb as the connection type.
  * Press the Ok button in the App to start the connection.
  * In the web HMI, click the italic "i" and select your app.
  * In the app, select "start service" to request permission to stream video.
  * The HMI will prompt you to give permission to the app to stream video. Click "Ok"
  * In the app, press "Start File Streaming". Depending on your video playback configuration, the video will begin playing in the web browser, or a gstreamer window open and begin playing the video.
