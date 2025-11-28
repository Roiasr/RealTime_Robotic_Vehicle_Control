# RealTime_Robotic_Vehicle_Control
A real-time robotic vehicle control system using joystick control with force-feedback and a VR visual interface to enhance field awareness and decision making.

## Motivation 
In hazardous or hard to reach environments, robotic vehicles are often used instead of humans. However, traditional remote-control systems provide **limited awareness**, making it difficult for the operator to understand the robot’s real time surroundings.

This project improves **human robot interaction** using:
- **VR first person visual interface** – view through the robot’s eyes  
- **Force-feedback joystick** – physical awareness from sensors  
- **Bidirectional communication** – real-time data flow between PC ↔ RPi ↔ Arduino

- ## Project Overview  
The system consists of **three parallel units**: 

#### 1️⃣ **Manual Control – Joystick to Robot Movement**
PC (joystick input)   → UDP →   Raspberry Pi (bridge)   → Serial →   Arduino (motor driver + servo) → Vehicle steering & wheel control 

#### 2️⃣ **Sensor Feedback – From Robot to Joystick**
Arduino (MPU6050 + LV-MaxSonar-EZ sensors)    → Serial →   Raspberry Pi (bridge)   → UDP →   PC (robot_control_dev.py) → Force-feedback activation on joystick (when needed) 

#### 3️⃣ **Live Video Streaming – First-Person VR View**
Camera connected to Raspberry Pi   → MJPEG stream over HTTP (TCP protocol)   → Displayed in Meta Quest 3 VR headset  

 **Electrical schematics and block diagrams are available in the `/diagrams/` folder.**

 ### 🧩 Hardware Used
| Component | Role |
|-----------|------|
| Raspberry Pi 4 | UDP/Serial bridge & video streaming |
| Arduino Uno | Controls DC motors & DS3235 servo |
| MPU6050 | Gyroscope/IMU for tilt detection |
| LV-MaxSonar-EZ | Distance measurement (obstacle detection) |
| Raspberry Pi Camera V2 8M | Live camera feed |
| Microsoft SideWinder Force Feedback 2 | Joystick with vibration feedback |
| L298N Motor Driver | Controls DC motors |
| AP63357 DC-DC convertor | Regulated 5V for servo |

### Programming Languages
| Language | Purpose |
|----------|--------|
| Python | PC client & RPi data bridge |
| C | Force feedback driver (.exe) |
| C++ / Arduino | Motor & sensor control logic |


## System Startup – Full Run Procedure
### Pre-flight Checklist

| Requirement | Status |
|-------------|--------|
| Same Wi-Fi / SSID | ✔ Required |
| SSH access to Raspberry Pi | ✔ Test with `ssh pi@<RPI_IP>` |
| Arduino recognized on RPi | ✔ Appears as `/dev/ttyACM0` |
| Joystick plugged into PC | ✔ PC terminal should show “Joystick initialized” |
| Video camera detected on RPi | ✔ `/dev/video0` exists |
| Ports used | 5005 (PC→RPi) · 5055 (RPi→PC) · 8080 (video) |

### Install Dependencies (one time only)
**On Raspberry Pi**
sudo apt update
sudo apt install python3-pip libcamera-dev
pip3 install --upgrade pyserial
**on pc**
pip install pygame

### Run Order
#### 1️⃣ Connect the Joystick
- Plug the joystick into the PC via USB.

#### 2️⃣ Flash Arduino 
- Upload Arduino_Script.ino using Arduino IDE (USB).

#### 3️⃣ Start the Raspberry Pi Bridge
- Open SSH session (MobaXterm or terminal):
ssh pi@<RPI_IP>
- Run the bidirectional bridge (RPi ⇄ Arduino ⇄ PC):
python3 bidirectional_bridge.py

#### 4️⃣ Run PC Control + Force Feedback
- In PowerShell / Terminal
python robot_control_dev.py

### 5️⃣ Start Live Video Stream (Second SSH tab)
- Open SSH session (MobaXterm or terminal):
ssh pi@<RPI_IP>
- MJPEG over HTTP on port 8080:
libcamerify ustreamer \
 --device=/dev/video0 \
 --host=0.0.0.0 \
 --port=8080 \
 --encoder=m2m-jpeg \
 --desired-fps=20 \
 --resolution=800x600 \
 --format=YUYV

 ### 6️⃣ View the Live VR Stream
- On **Meta Quest 3 Browser** 
  http://<RPI_IP>:8080/

  
