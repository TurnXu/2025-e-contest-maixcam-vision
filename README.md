# 2025 Electronic Design Contest E Problem Vision System Based on MaixCAM

A MaixCAM-based embedded vision project for the 2025 Electronic Design Contest E problem. The project provides a lightweight visual interface layer and serial communication protocol for competition-oriented target detection, coordinate output, and main-controller interaction.

## Project Overview

This project focuses on the vision component of an electronic design contest system. MaixCAM is used as an embedded vision processor that captures image information, displays visual interaction elements, and sends target information to the main controller through a serial protocol.

The repository currently includes a MaixCAM GUI helper module, a serial communication protocol module, Maix application configuration, a packaged `dist` directory, and a demonstration video.

## Contest Task Description

For an Electronic Design Contest E problem, the vision subsystem usually needs to provide reliable perception information to the control subsystem.

Typical responsibilities include:

- Capturing target information from the camera.
- Locating key points or geometric centers.
- Displaying status and debug information.
- Sending coordinates to STM32 or another main controller.
- Supporting closed-loop control for motors, servos, or laser-point adjustment.

## Hardware Platform

```text
Vision processor: MaixCAM
Main controller: STM32 / ESP32 / Arduino / Raspberry Pi / other embedded boards
Peripherals: camera module, display, touchscreen, UART, motor driver, servo, laser module
```

## Vision Solution

Recommended workflow:

```text
Camera image acquisition
-> image preprocessing
-> target or marker detection
-> coordinate calculation
-> display overlay / touch interaction
-> UART packet construction
-> main controller parsing
-> actuator control
```

The current repository provides the GUI and communication infrastructure. Task-specific image processing can be added on top of these modules.

## Vision Algorithms

Depending on the final task requirement, the vision algorithm may include:

- Color threshold segmentation.
- Laser-point detection.
- Rectangle center detection.
- Shape recognition.
- Line extraction.
- Region-of-interest filtering.
- Coordinate smoothing.
- Frame-to-frame stability filtering.

For contest deployment, the algorithm should prioritize robustness, real-time performance, and easy parameter tuning.

## Functional Implementation

### GUI Module

File:

```text
gui.py
```

Main responsibilities:

- Initialize MaixCAM display and touchscreen.
- Create touchable UI elements.
- Register callbacks for UI items.
- Display text labels and visual feedback.
- Map touchscreen coordinates to image coordinates.

### Serial Protocol Module

File:

```text
serial_protocol.py
```

Main responsibilities:

- Open UART serial connection.
- Build binary data packets.
- Send laser-point coordinates.
- Send rectangle-center coordinates.
- Close serial connection safely.

The packet format is:

```text
HEAD + TAG + X_LOW + X_HIGH + Y_LOW + Y_HIGH + TAIL
```

Default values:

```text
HEAD = 0xAA
TAIL = 0x55
TAG_LASER_POINT = 0x01
TAG_RECT_CENTER = 0x00
```

## Wiring Instructions

Typical UART wiring:

```text
MaixCAM TX  -> Main Controller RX
MaixCAM RX  -> Main Controller TX
MaixCAM GND -> Main Controller GND
```

Important notes:

- TX and RX must be crossed.
- MaixCAM and the main controller must share a common ground.
- Confirm voltage compatibility before wiring.
- Use a stable power supply when motors or servos are running.
- Keep camera and wiring fixed during final testing.

## Code Running Method

### 1. Prepare MaixCAM Environment

Install and configure:

```text
MaixVision
MaixPy
MaixCAM firmware
USB serial driver
```

### 2. Enter the Local Project

```bash
cd "D:/github/2025电赛e题视觉部分Maixcam"
```

### 3. Check Application Configuration

```text
app.yaml
```

### 4. Upload to MaixCAM

Upload the Python files to MaixCAM using MaixVision or the official deployment workflow.

Core files:

```text
gui.py
serial_protocol.py
```

### 5. Configure Serial Port

The serial protocol defaults to:

```python
SerialProtocol(port="/dev/ttyS0", baudrate=9600)
```

Modify the port and baud rate according to the actual hardware connection.

### 6. Verify Communication

Send test coordinates to the main controller and confirm that the parser receives:

```text
0xAA, tag, x_low, x_high, y_low, y_high, 0x55
```

## File Structure

```text
.
+-- README.md
+-- app.yaml
+-- demo.mp4
+-- gui.py
+-- serial_protocol.py
+-- dist
```

## Parameter Tuning Suggestions

- Tune image thresholds under the actual contest lighting environment.
- Limit the region of interest to improve speed and reduce false detections.
- Add filtering for small noisy regions.
- Use frame-to-frame smoothing for coordinate output.
- Verify UART packet parsing before full-system testing.
- Test vision, communication, and control modules separately before integration.

## Common Problems

- Serial communication failure is usually caused by reversed TX/RX wiring, missing common ground, baud-rate mismatch, wrong serial port, or inconsistent packet parsing.
- Incorrect touchscreen coordinates are usually related to display scaling, image coordinate mapping, or background image size.
- Unstable detection is usually caused by lighting changes, reflections, camera shaking, or untuned threshold parameters.

## License

This project is intended for contest preparation, embedded vision learning, and engineering demonstration. Please follow the contest rules and repository license when using or modifying this project.
