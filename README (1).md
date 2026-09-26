# Gesture-Controlled 4 DOF Robotic Arm Using MPU6050

> A wireless, hand-gesture-controlled robotic arm developed as a
> Mechatronics Engineering project.

## Project Overview

This project presents the design and implementation of a
4-degree-of-freedom (4 DOF) robotic arm controlled by hand gestures. An
MPU6050 inertial measurement sensor captures hand orientation and
motion. A transmitter NodeMCU processes the sensor readings, classifies
gestures, and sends commands wirelessly using ESP-NOW to a receiver
NodeMCU. The receiver drives four servo motors for the base, shoulder,
elbow, and gripper.

The prototype combines sensor interfacing, embedded programming, signal
processing, wireless communication, and mechatronic actuation in a
compact educational system.

## Project Team

-   **Ganapathy M**
-   **Pravenn Kumar B**
-   **Manoj B**

**Department:** Mechatronics Engineering\
**Institution:** Coimbatore Institute of Engineering and Technology,
Coimbatore\
**Project report:** May 2026

## Key Features

-   Hand-motion input using an MPU6050 accelerometer and gyroscope
-   Two NodeMCU ESP8266 boards: transmitter and receiver
-   Direct wireless communication using ESP-NOW (no router or internet
    required)
-   Four servo-controlled motions
-   Sensor calibration and low-pass filtering
-   Threshold-based gesture recognition
-   Servo smoothing and angle limits to reduce abrupt motion

## System Architecture

``` text
HAND MOVEMENT
     |
     v
MPU6050 Sensor
(Accelerometer + Gyroscope)
     |
     v
TRANSMITTER NODEMCU (ESP8266)
Calibration -> Filtering -> Gesture Classification
     |
     v
ESP-NOW Wireless Command Packet
     |
     v
RECEIVER NODEMCU (ESP8266)
Command Decode -> Angle Limits -> Servo Smoothing
     |
     v
4 SERVO MOTORS
Base | Shoulder | Elbow | Gripper
     |
     v
ACRYLIC 4 DOF ROBOTIC ARM
```

## Hardware Components

  Component                         Quantity Purpose
  ------------------------------- ---------- --------------------------------------------
  NodeMCU ESP8266                          2 Transmitter and receiver controllers
  MPU6050 sensor module                    1 Measures hand motion and orientation
  Hobby servo motors                       4 Control base, shoulder, elbow, and gripper
  Acrylic 4 DOF robotic arm kit            1 Mechanical structure
  5 V external power supply                1 Supplies servo power
  Jumper wires                       Several Electrical connections
  Breadboard                               1 Prototyping and testing

## Four Degrees of Freedom

1.  **Base rotation:** rotates the arm left and right.
2.  **Shoulder movement:** lifts or lowers the arm.
3.  **Elbow movement:** bends or extends the arm.
4.  **Gripper operation:** opens and closes to hold or release small
    objects.

## Gesture Design and Mapping

  -----------------------------------------------------------------------
  Hand gesture / sensor cue           Robotic action
  ----------------------------------- -----------------------------------
  Left tilt (negative roll)           Base rotates left

  Right tilt (positive roll)          Base rotates right

  Forward tilt (positive pitch)       Shoulder moves up

  Backward tilt (negative pitch)      Shoulder moves down

  Clockwise twist (positive angular   Elbow moves up
  velocity)                           

  Counter-clockwise twist (negative   Elbow moves down
  angular velocity)                   

  Quick shake (acceleration spike)    Toggle gripper open/close
  -----------------------------------------------------------------------

The design separates slow tilting gestures from quick twist and shake
gestures to reduce overlap between commands. The user returns the hand
to a neutral position between discrete commands.

## Pin Connections

### Transmitter: MPU6050 to NodeMCU

  MPU6050 signal   NodeMCU pin
  ---------------- -------------
  SDA              D2 / GPIO4
  SCL              D1 / GPIO5
  VCC              3.3 V
  GND              GND

### Receiver: Servos to NodeMCU

  Servo      Function            NodeMCU pin
  ---------- ------------------- --------------
  Base       Base rotation       D5 / GPIO14
  Shoulder   Shoulder movement   D6 / GPIO12
  Elbow      Elbow movement      D7 / GPIO13
  Gripper    Gripper control     D1 / GPIO5\*

\*The report notes that the gripper pin can be changed if D1 is already
used elsewhere. Verify the final practical wiring and ensure the
selected GPIO is suitable.

### Power and Grounding

-   Power the servos from a separate regulated 5 V supply; do not draw
    the combined servo current from the NodeMCU.
-   Connect the external servo supply ground and NodeMCU ground together
    to provide a common signal reference.
-   Check wiring, connectors, and power stability if the servos jitter
    or the controller resets.

## Working Principle

1.  **Sensing:** The MPU6050 measures acceleration and angular velocity
    as the operator moves their hand.
2.  **Calibration:** The transmitter samples the sensor in a neutral
    position and calculates offsets.
3.  **Filtering:** Sensor readings are filtered to reduce noise and
    small unwanted fluctuations.
4.  **Gesture classification:** The controller compares pitch, roll, and
    angular motion against predefined thresholds.
5.  **Wireless transmission:** A recognized gesture is encoded into a
    compact command packet and sent using ESP-NOW.
6.  **Command decoding:** The receiver interprets the command and
    updates the target angle of the relevant servo.
7.  **Actuation:** Angle limits and gradual servo movement are applied
    before the arm performs the action.

## Software Logic

### Transmitter flow

``` text
Initialize NodeMCU, I2C, MPU6050, and ESP-NOW
Pair/register receiver peer
Calibrate sensor in neutral hand position
Loop:
  Read accelerometer and gyroscope
  Apply calibration offsets and filtering
  Calculate pitch, roll, and angular motion
  Classify gesture using thresholds
  If valid and not locked out:
    Send command packet
    Start lockout until hand returns to neutral
```

### Receiver flow

``` text
Initialize NodeMCU and ESP-NOW
Attach four servos and set home positions
Loop:
  Check for received command
  Update base / shoulder / elbow / gripper target
  Apply safe angle limits
  Move servos gradually toward target positions
```

## Experimental Results Summary

The project report describes successful conversion of the main hand
gestures into corresponding robotic-arm movements during testing.

  Test area                Observation reported
  ------------------------ ---------------------------------------------
  Gesture detection        Major hand gestures detected
  Wireless communication   ESP-NOW communication stable with low delay
  Servo movement           Smoother and more stable after smoothing
  Base and shoulder        Higher accuracy and reliable response
  Twist and shake          Needed careful threshold tuning
  False triggers           Reduced after filtering and lockout logic
  System stability         Good with external servo power
  Overall                  Suitable for educational demonstrations

These are qualitative observations from the project report; numerical
accuracy, latency, and payload measurements are not specified there.

## Limitations

-   Discrete gesture commands rather than continuous motion tracking
-   Accuracy depends on hand stability and calibration
-   Sensor noise and drift can cause false detection
-   User needs to return to neutral between commands
-   Acrylic structure and hobby servos limit payload, speed, and
    precision
-   Wireless range is short-range
-   Complex simultaneous gestures may confuse the threshold classifier

## Future Scope

-   Continuous motion tracking
-   Inverse kinematics for position-based control
-   Machine-learning-based gesture recognition
-   Mobile or web application integration
-   Rechargeable battery operation
-   Higher-precision servo motors
-   Camera-based object detection
-   LCD/OLED system feedback
-   IoT-based monitoring and control
-   More degrees of freedom and closed-loop feedback
-   Voice-controlled operation

## Troubleshooting

-   **Unstable MPU6050 readings:** check I2C wiring, power, and ground;
    repeat calibration.
-   **ESP-NOW packets not received:** verify the receiver MAC address
    and communication initialization.
-   **Servo jitter or controller resets:** use a suitable external 5 V
    supply and common ground.
-   **Arm moves in the wrong direction:** adjust the software angle
    mapping and limits.
-   **Gestures are missed:** review thresholds and gesture hold/lockout
    timing; test with deliberate movements.

## Repository Structure

Suggested organization as you add your project files:

``` text
4-DOF-Gesture-Control-Robotic-Arm/
├── README.md
├── firmware/
│   ├── transmitter/
│   └── receiver/
├── hardware/
│   ├── wiring-diagram/
│   └── component-list/
├── media/
│   ├── photos/
│   └── demo-video/
└── docs/
    └── project-report.pdf
```

Add your actual transmitter and receiver source code, circuit images,
project photos, and a PDF copy of the report to the relevant folders.
The folder names above are a suggested layout; add only files you have.

## References

The project report lists the following technical references:

1.  Rowberg --- MPU6050 motion sensor documentation.
2.  Espressif Systems --- ESP-NOW Programming Guide and Arduino ESP-NOW
    API Reference.
3.  InvenSense --- MPU-6000 / MPU-6050 Register Map.
4.  TDK InvenSense --- MPU6050 sensor specifications and
    evaluation-board resources.
5.  Research references on IMU noise modeling and MPU6050 filtering, as
    listed in the project report.

## Author

**Ganapathy M**\
B.E. Mechatronics Engineering\
Coimbatore Institute of Engineering and Technology
