## 4.8. Deployment View

The deployment view takes into account the system's requirements such as system availability, reliability (fault tolerance), performance (throughput), and scalability. This view maps the various elements identified in the logical, process, and development views—networks, processes, tasks, and objects—onto the processing nodes.
The deployment diagram is used for modeling the static deployment view of a system.
The figure below depicts the deployment diagram for SDL system.

|||
##### Deployment View Diagram
![Deployment View](./assets/image24.png)
|||

***Elements description***

#### Mobile Device
  - *Short Description:*
    - The SDL application model permits multiple applications to be concurrently active and connected to the HU.
    - A few of those applications may interact with the user at a time using the HMI (depending on HMI). 
    - SDL uses the concept of HMI Levels to describe the current state of the application with regards to the level at which the head unit can communicate with it (and vice versa). 
  - *Relations:*
    - Receives policies updates from **Cloud Server**
    - Sends statistics to **Cloud Server**. 
  - *Requirements:*
    - Android OS or iOS. 

#### Head Unit
  - *Short Description:*
  - HU HMI allows the user/driver to interact with the vehicle.
    - This interface includes:
    - A set of presets
    - Media buttons (seek forward/backward, tune up/down, and play/pause)
    - Menu items
    - Graphic user interface
    - Voice commands, etc.
  - The HU HMI Handler interfaces with SDL Core to support the API functionality. 
  - *Relations:*
    - Communicates with applications on **Mobile Device**
  - *Requirements:*
    - N/A

#### CloudServer
  - *Short Description:*
    - A Server that provides information about:
      - Which applications are allowed to run in vehicle 
      - What interfaces application is allowed to use. 
    - In addition, server provides:
      - System configuration, including the time of the next file update
      - Some important server information to the back end user 
  - *Relations:*
    - Sends policies updates to **Mobile Device**.
    - Receives statistics from **Mobile Device**. 
  - *Requirements:*
    - N/A 
