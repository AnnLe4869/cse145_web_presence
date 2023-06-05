# Full-body tracking

## Overview

Full body tracking systems have many use cases such as virtual reality and rigging for CGI video productions. There are several existing techniques that deal with tracking; however current methods have severe limitations which we aim to address.

The first and most common body tracking method is visual tracking where the users wear several reflective markers on the body that is seen by an external capture device such as a camera. This capture device is able to then take the location of where each marker is and calculate the body location. Despite this being a commonly used method, there are major limitations including a steep initial setup cost, time, and labor as well as a limited capture space. This is compounded by occlusion, or broken line of sight between the capture device and person which creates the need for even more capture devices.

The second method is using several very accurate inertial measurement units (IMU) and estimating body movement. This has advantages over visual tracking where the user does not have to consider the location of capture devices. However, its core disadvantage is the accumulation of error over time. In order to attain the position of the body, an integration needs to be done relative to acceleration which causes positional drift to occur after around 5 to 10 minutes. Some companies have used high-precision IMUs to address this issue but such devices are expensive and still prone to error after enough time.

Our project plan is to improve on both solutions by using ultra-wide band (UWB) trackers. Moreover, we plan to use ultra-wide band emitters and receivers to create a closed loop system - that is, a system that uses multiple sources of data to implement robust error correction - minimizing the IMU error build up and greatly extending the possible capture time. This will also allow a user to operate in any space without the need for prior set up and any associated costs.

## Equipment details

The equipments needed for this projects are:

- UWB anchor: there are four(4) of these UWB anchors, each stand in one corner of the room. The UWB anchors will listen to the UWB emitted from the tag
- Raspberry Pi 3:  there are four(4), with each attaches to one anchor. The Raspberry Pi handles the data collected from the UWB anchor: it calculate the location of the tag from the response wave. Additionally, it transmits the calculated location of a tag via Ethernet cable back to the server
  ![UWB anchor and Raspberry Pi 3 underneath it](./images/anchor/IMG_20230604_213424.jpg)
- Tag (also called tracker): there are six(6) of them. These are devices that are attached to the body. Each has an IMU inside that is capable of measuring the rotation and acceleration of the body part it attaches to. These data are then transmitted via Wifi back to the server. The tag is also responsible for sending out UWB wave to the UWB anchors
  ![Tag](./images/tag/IMG_20230604_213535.jpg)
- Good UWB emitter: these are devices that can emits UWB wave very reliably. We use this as baseline to compare how the tag UWB perform with the emitter
  ![Good UWB emitter](images/high_precision_uwb/IMG_20230604_213924.jpg)
- High precision IMU: this is the IMU that is super accurate. We attach this to the back of a tag and compare the data between the tag and this accurate IMU, and from there calculate the variance. This is needed for the fusion between IMU and UWB
  ![High accuracy IMU](images/high_precision_imu/IMG_20230604_214008.jpg)
- Good IMU: there are 6 of them, and we use these as ground truth to compare how the tags in action work against them. These are commercial products and hence more obtainable
  ![Good IMU](images/good_imu/imu_good.jpg)
- Server: a computer that listen to all data from the Raspberry Pi and tags

## Teams

Everyone:
    - Set-up of the ULoc system environment
    - Evaluated current hardware used
    - Examined current codebase for allowing UWB tracking
    - Gathered visual and quantitative data on UWB positions
Anh Le:
    - Set up all IMU tags so that they can sync their time between each other - with small enough difference (less than 500 microseconds between any tags)
    - Set up the UWB to get the data in sync with the data flow from IMUs
Branson Beihl:
    - Learned about factor graphs and GTSAM
    - Learned about inverse kinematic methods
    - Fine tune the IMUs' data using data from an accurate IMU
Danny Vo:
    - Learned about inverse kinematic methods and PE-DLS
    - Implement & test PE-DLS IK solver from unity to python to use for custom points from Factor Graph
Michael Shao:
    - Worked with Anh to conceive the IMU sync system.
    - Generate factor graph to sensor fuse the IMU & UWB positional data

## Repository organization

The codes for this project are separated into multiple small repos:

- ULoc_Data_Collect_Sync: this repository is the code to run the server. It starts the server, listen to the data from raspberry pi and the tag, and write them down to log files
- ULoc_ESP_UDP: this repository is the code that run in the tag. Responsible to enable the wifi transmission, listen to UWB, listen to the IMU
- uloc_firmware: this repository contains code that run the anchor
- data_collection_template: contains the code that run the raspberry pi
- uloc_post_processing: contains some algorithms and code to process the UWB data
