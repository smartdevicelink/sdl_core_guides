## Initial Configuration

### Install Packages

```
sudo apt-get install git cmake build-essential libavahi-client-dev libsqlite3-dev chromium-browser libssl-dev libudev-dev libgtest-dev libbluetooth3 libbluetooth-dev bluez-tools gstreamer1.0* libpulse-dev
```

```
sudo apt-get update
sudo apt-get upgrade
sudo ldconfig
```

### Clone the SDL Core Repository

Clone the SDL Core [repository](https://github.com/smartdevicelink/sdl_core)

```
git clone https://github.com/smartdevicelink/sdl_core.git
```

CD into sdl_core/ and checkout the [master branch](https://github.com/smartdevicelink/sdl_core/tree/master)

```
git checkout origin/master
```

### Clone the SDL HMI Repository

Clone the Web HMI [repository](https://github.com/smartdevicelink/sdl_hmi)

```
git clone https://github.com/smartdevicelink/sdl_hmi.git
```

CD into sdl_hmi/ and checkout the [master branch](https://github.com/smartdevicelink/sdl_hmi/tree/master)

```
git checkout origin/master
```

### Setup the Build Environment

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

### GSTREAMER Setup

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

## Pipe Streaming

### Configuration (smartDeviceLink.ini)
In the build folder directory, open bin/smartDeviceLink.ini in a text editor and the make the following changes:
```
;VideoStreamConsumer = socket
;AudioStreamConsumer = socket
;VideoStreamConsumer = file
;AudioStreamConsumer = file
VideoStreamConsumer = pipe
AudioStreamConsumer = pipe
```

### HMI Modifications
Using pipe streaming may require a modification to the SDL HMI.

#### VIDEO

### Video Stream Pipe

After you start SDL Core, cd into the bin/storage directory and there should be a file named "video_stream_pipe". Use the gst-launch command that worked for your environment and set file source to the video_stream_pipe file. You should see “setting pipeline to PAUSED” and “Pipeline is PREROLLING”.

#### Raw H.264
```
gst-launch-1.0 filesrc location=$SDL_BUILD_PATH/bin/storage/video_stream_pipe ! decodebin ! videoconvert ! xvimagesink sync=false
```

#### H.264 over RTP (Ubuntu 16.04+, GStreamer 1.4+)
```
gst-launch-1.0 filesrc location=$SDL_BUILD_PATH/bin/storage/video_stream_pipe ! "application/x-rtp-stream" ! rtpstreamdepay ! "application/x-rtp,media=(string)video,clock-rate=90000,encoding-name=(string)H264" ! rtph264depay ! "video/x-h264, stream-format=(string)avc, alignment=(string)au" ! avdec_h264 ! videoconvert ! ximagesink sync=false
```

### Audio Stream Pipe

#### RAW PCM

```
gst-launch-1.0 filesrc location=$SDL_BUILD_PATH/bin/storage/audio_stream_pipe ! audio/x-raw,format=S16LE,rate=16000,channels=1 ! pulsesink
```

## Socket Streaming

### Configuration (smartDeviceLink.ini)
In the build folder directory, open `bin/smartDeviceLink.ini` in a text editor and the make the following changes:
```
; Socket ports for video and audio streaming
VideoStreamingPort = 5050
AudioStreamingPort = 5080
...
VideoStreamConsumer = socket
AudioStreamConsumer = socket
;VideoStreamConsumer = file
;AudioStreamConsumer = file
;VideoStreamConsumer = pipe
;AudioStreamConsumer = pipe
```

### HMI Modifications
Using socket streaming may require a modification to the SDL HMI.

#### VIDEO
Comment out the following lines in `app/model/sdl/Abstract/Model.js`:
```
//  SDL.SDLModel.playVideo(appID);
```

#### AUDIO
Comment out the following lines in `app/model/sdl/Abstract/Model.js`:
```
//  SDL.StreamAudio.play(
//      SDL.SDLController.getApplicationModel(appID).navigationAudioStream
//  );
```

### Video Stream Socket

#### Raw H.264
```
gst-launch-1.0 souphttpsrc location=http://127.0.0.1:5050 ! decodebin ! videoconvert ! xvimagesink sync=false
```

#### H.264 over RTP (Ubuntu 16.04+, GStreamer 1.4+)
```
gst-launch-1.0 souphttpsrc location=http://127.0.0.1:5050 ! "application/x-rtp-stream" ! rtpstreamdepay ! "application/x-rtp,media=(string)video,clock-rate=90000,encoding-name=(string)H264" ! rtph264depay ! "video/x-h264, stream-format=(string)avc, alignment=(string)au" ! avdec_h264 ! videoconvert ! ximagesink sync=false
```

### Audio Stream Socket

#### RAW PCM

```
gst-launch-1.0 souphttpsrc location=http://127.0.0.1:5080 ! audio/x-raw,format=S16LE,rate=16000,channels=1 ! pulsesink
```

# Start SDL Core
In the build folder

```
cd bin
./start.sh
```
!!! NOTE
If you want to use a USB connection, you must run
```
sudo ./start.sh
```
!!!

### Start the web HMI

CD into the HMI repository and run
```
chromium-browser index.html
```

### Start Video or Audio Stream

[iOS Video Streaming Guide](../../iOS/mobile-navigation/video-streaming/)

[Android Video Streaming Guide](../../Android/mobile-navigation/video-streaming/)
