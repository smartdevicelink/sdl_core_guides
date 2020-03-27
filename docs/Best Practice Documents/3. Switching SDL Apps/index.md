# 3. Switching SDL Apps

## 1. Overview
This chapter describes the sequence of switching SDL Apps.

In addition, regarding the behavior of "Pressing other SDL App Icon on the HU screen" and "The launch by pressing HardSW" please refer to the following:

<ol>
"2.1. Press the SDL App Icon on the SDL Menu screen"<br>
"2.2. Launch by  Voice Recognition"<br>
"2.3. Launch by choosing the Audio source on the Audio screen"<br>
"2.4. Launch by HardSW"<br>
</ol>

In this chapter, the overall sequence of switching SDL Apps for each type is described.

## 2. Background/Purpose/Reason for Standardization
Currently, switching to other SDL App while an (existing) SDL App is running, is an SDL standard behavior.
However, since the behavior of switching Apps is not explicitly defined in the SDL standard specification, it is necessary for the OEMs to define it by themselves.
Hence, the purpose of this document is to standardize such cases/issues using the TOYOTA specification, in order to be able to contribute to the SDL Ecosystem.

## 3. Function Details
### 3.1. Function overview
Table 1 shows the 9 patterns for switching SDL Apps.

**Table1.** Sequence pattern of switching SDL Apps
<table><tr><th><div align="center"> No. </div></th><th><div align="center"> 1st Launched App </div></th><th><div align="center"> 2nd launched App </div></th></tr><tr><td><div align="center"> 1 </div></td><td rowspan="3"><div align="left"> "MEDIA" type SDL App </div></td><td><div align="left"> "MEDIA" type SDL App </div></td></tr><tr><td><div align="center"> 2 </div></td><td><div align="left"> "NAVIGATION"/"PROJECTION" type SDL App </div></td></tr><tr><td><div align="center"> 3 </div></td><td><div align="left"> Normal type SDL App </div></td></tr><tr><td><div align="center"> 4 </div></td><td rowspan="3"><div align="left"> "NAVIGATION"/"PROJECTION" type SDL App </div></td><td><div align="left"> "MEDIA" type SDL App </div></td></tr><tr><td><div align="center"> 5 </div></td><td><div align="left"> "NAVIGATION"/"PROJECTION" type SDL App </div></td></tr><tr><td><div align="center"> 6 </div></td><td><div align="left"> Normal type SDL App </div></td></tr><tr><td><div align="center"> 7 </div></td><td rowspan="3"><div align="left"> Normal type SDL App </div></td><td><div align="left"> "MEDIA" type SDL App </div></td></tr><tr><td><div align="center"> 8 </div></td><td><div align="left"> "NAVIGATION"/"PROJECTION" type SDL App </div></td></tr><tr><td><div align="center"> 9 </div></td><td><div align="left"> Normal type SDL App </div></td></tr></table>

App types are determined by `AppHMIType` in Table2 :

**Table2.** AppType Categorized by each AppHMIType
<table><tr><th><div align="center"> App Type </div></th><th><div align="center"> AppHMIType </div></th></tr><tr><td rowspan="9"><div align="left"> Normal type App </div></td><td><div align="left"> DEFAULT </div></td></tr><tr><td><div align="left"> COMMUNICATION </div></td></tr><tr><td><div align="left"> MESSAGING </div></td></tr><tr><td><div align="left"> INFORMATION </div></td></tr><tr><td><div align="left"> SOCIAL </div></td></tr><tr><td><div align="left"> BACKGROUND_PROCESS </div></td></tr><tr><td><div align="left"> TESTING </div></td></tr><tr><td><div align="left"> SYSTEM </div></td></tr><tr><td><div align="left"> REMOTE_CONTROL </div></td></tr><tr><td><div align="left"> MEDIA type App </div></td><td><div align="left"> MEDIA </div></td></tr><tr><td rowspan = "2"><div align="left"> NAVIGATION / PROJECTION type App </div></td><td><div align="left"> NAVIGATION </div></td></tr><tr><td><div align="left"> PROJECTION </div></td></tr></table>

## 4. Differences from SDL standard specification
There is no provided description about the sequences of switching between the SDL Apps in the SDL official documents.
Therefore, all of the following sequences  describe in "5. Sequence Diagrams" differ from the existing SDL standard specifications.

## 5. Sequence Diagrams
### 5.1. Switching from "MEDIA" type SDL App to "MEDIA" type SDL App

|||
**Figure1.** Sequence of switching from "MEDIA" type SDL App to "MEDIA" type SDL App
![figure1.from_media_sdl_app_to_media_sdl_app.png](./assets/figure1.from_media_sdl_app_to_media_sdl_app.png)
|||

### 5.2. Switching from "MEDIA" type SDL App to "NAVIGATION"/"PROJECTION" type SDL App

|||
**Figure2.** Sequence of switching from "MEDIA" type SDL App to "NAVIGATION/PROJECTION" type SDL App
![figure2.from_media_sdl_app_to_navipro_sdl_app.png](./assets/figure2.from_media_sdl_app_to_navipro_sdl_app.png)
|||

### 5.3. Switching from "MEDIA" type SDL App to Normal type SDL App

|||
**Figure3.** Sequence of switching from "MEDIA" type SDL App to Normal type SDL App
![figure3.from_media_sdl_app_to_other_sdl_app.png](./assets/figure3.from_media_sdl_app_to_other_sdl_app.png)
|||

### 5.4. Switching from "NAVIGATION"/"PROJECTION" type SDL App to "MEDIA" type SDL App

|||
**Figure4.** Sequence of switching from "NAVIGATION/PROJECTION" type SDL App to "MEDIA" type SDL App
![figure4.from_navipro_sdl_app_to_media_sdl_app.png](./assets/figure4.from_navipro_sdl_app_to_media_sdl_app.png)
|||

### 5.5. Switching from "NAVIGATION"/"PROJECTION" type SDL App to "NAVIGATION"/"PROJECTION" type SDL App

|||
**Figure5.** Sequence of switching from "NAVIGATION/PROJECTION" type SDL App to "NAVIGATION/PROJECTION" type SDL App
![figure5.from_navipro_sdl_app_to_navipro_sdl_app.png](./assets/figure5.from_navipro_sdl_app_to_navipro_sdl_app.png)

### 5.6. Switching from "NAVIGATION"/"PROJECTION" type SDL App to Normal type SDL App

|||
**Figure6.** Sequence of switching from "NAVIGATION/PROJECTION" type SDL App to Normal type SDL App
![figure6.from_navipro_sdl_app_to_other_sdl_app.png](./assets/figure6.from_navipro_sdl_app_to_other_sdl_app.png)
|||

### 5.7. Switching from Normal type SDL App to "MEDIA" type SDL App

|||
**Figure7.** Sequence of switching from Normal type SDL App to "MEDIA" type SDL App
![figure7.from_other_sdl_app_to_media_sdl_app.png](./assets/figure7.from_other_sdl_app_to_media_sdl_app.png)
|||

### 5.8. Switching from Normal SDL App to "NAVIGATION"/"PROJECTION" type SDL App

|||
**Figure8.** Sequence of switching from Normal type SDL App to "NAVIGATION/PROJECTION" type SDL App
![figure8.from_other_sdl_app_to_navipro_sdl_app.png](./assets/figure8.from_other_sdl_app_to_navipro_sdl_app.png)
|||

### 5.9. Switching from Normal type SDL App to Normal type SDL App

|||
**Figure9.** Sequence of switching from Normal type SDL App to Normal type SDL App
![figure9.from_other_sdl_app_to_other_sdl_app.png](./assets/figure9.from_other_sdl_app_to_other_sdl_app.png)<br>
|||

## 6. Impacted Platforms
Changes impact the following platform/s:
- HMI