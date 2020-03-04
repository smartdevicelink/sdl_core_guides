## Initial Configuration

### SDL Core Setup

If you don't already have Core built, you can follow the [install and run guide](../../getting-started/install-and-run/).

### SDL HMI Setup

If you don't already have the SDL HMI, download it via the following command

```
git clone -b master https://github.com/smartdevicelink/sdl_hmi.git
```

### HMI Modifications
Using either pipe or socket streaming may require a modification to the SDL HMI.

#### VIDEO
Comment out the following line in `app/model/sdl/Abstract/Model.js`:
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

### GSTREAMER Setup

First, we must determine what gstreamer command works in your environment

Start by finding a raw h.264 file ([Example](https://support.apple.com/library/APPLE/APPLECARE_ALLGEOS/HT1425/sample_iPod.m4v.zip)) and determine which of the these gstreamer commands successfully plays the example video

- Ubuntu 14.04+
```
gst-launch-1.0 filesrc location=/path/to/h264/file ! decodebin ! videoconvert ! xvimagesink sync=false
```
or
```
gst-launch-1.0 filesrc location=/path/to/h264/file ! decodebin ! videoconvert ! ximagesink sync=false
```

If you're using tcp, you can connect the stream directly with your phone's IP address using
```
gst-launch-1.0 tcpclientsrc host=<Device IP Address> port=3000 ! decodebin ! videoconvert ! ximagesink sync=false
```

## Pipe Streaming

### Configuration (smartDeviceLink.ini)
In the Core build folder, open `bin/smartDeviceLink.ini` and the make the following changes:
```
;VideoStreamConsumer = socket
;AudioStreamConsumer = socket
;VideoStreamConsumer = file
;AudioStreamConsumer = file
VideoStreamConsumer = pipe
AudioStreamConsumer = pipe
```

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
In the Core build folder, open `bin/smartDeviceLink.ini` and the make the following changes:
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

## Video Streaming States

This section describes how Core manages the streaming states of mobile applications. Only one application may stream video at a time, but audio applications may stream while in the LIMITED state with other applications.

When an app is moved to HMI level `FULL`:
* All non-streaming applications go to HMI level `BACKGROUND`
* All apps with the same App HMI Type go to `BACKGROUND`
* Streaming apps with a different App HMI Type that were in `FULL` go to `LIMITED`

When an app is moved to HMI level `LIMITED`:
* All non-streaming applications keep their HMI level
* All applications with a different App HMI Type keep their HMI level
* Applications with the same App HMI Type go to `BACKGROUND`

### Start the Web HMI

CD into the HMI repository and run
```
chromium-browser index.html
```

### Additional Resources

!!! NOTE
Livio provides an [example video streaming android application](https://github.com/livio/sdl_video_streaming_android_sample).
!!!

[iOS Video Streaming Guide](../../../iOS/video-streaming-for-navigation-apps/video-streaming/)

[Android Video Streaming Guide](../../../android/video-streaming-for-navigation-apps/video-streaming/)
