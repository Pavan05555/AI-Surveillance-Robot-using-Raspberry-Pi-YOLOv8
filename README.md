**AI Surveillance Robot using Raspberry Pi & YOLOv8**

This project is an AI-based mobile surveillance robot built on Raspberry Pi 4. The system can move autonomously, detect persons and vehicle number plates in real time, avoid obstacles, and stream processed video over a local web interface. Detected faces and plates are automatically cropped and saved as evidence images for later review, making the platform useful for low-cost security and monitoring applications.

The robot performs real-time object detection using custom YOLOv8 models trained for person detection (person.pt) and Indian vehicle number plate detection (plate.pt). Whenever a detection occurs, the system crops and stores the relevant regions: person images are saved in a persons/ folder and number plate images in a plates/ folder. For plates, the system additionally uses Tesseract OCR to extract the text and log it for future reference.

Live video streaming is provided through a Flask-based web dashboard. The processed frames, with bounding boxes, labels, and OCR text overlaid, are broadcast as an MJPEG stream. For security reasons, the web interface is only accessible to devices on the same Wi-Fi network as the Raspberry Pi. This makes it suitable for indoor/outdoor local surveillance without exposing the feed over the public internet.

The robot’s navigation relies on a combination of motor control and obstacle sensing. An HC-SR04 ultrasonic sensor measures the distance to obstacles (up to around 4 m), enabling safe stopping or direction changes when something is too close. An L298N dual H-bridge motor driver controls four DC motors arranged on a 4WD chassis, providing differential drive for forward, backward, and turning motions. An SG90 servo motor is used to pan or tilt the Raspberry Pi camera, increasing the coverage area without needing to reposition the entire robot.

At the core of the system is the Raspberry Pi 4 running Raspberry Pi OS. It handles camera capture, YOLOv8 inference for persons and number plates, OCR processing via Tesseract, ultrasonic distance measurement, GPIO-based motor and servo control, and the Flask web server for streaming. Perception is handled by the Raspberry Pi Camera connected over CSI, while the AI pipeline uses YOLOv8 (Ultralytics) for detection and pytesseract for reading plate numbers from cropped images.

The hardware stack consists of the Raspberry Pi 4, Raspberry Pi Camera Module, L298N motor driver, four DC motors on a 4WD chassis, HC-SR04 ultrasonic sensor, SG90 servo motor, and appropriate 5V/12V power sources and regulators. On the software side, the project uses Python 3, Ultralytics YOLOv8, OpenCV (cv2), Flask, NumPy, pytesseract, and standard Python libraries like threading and collections.deque for multithreading and frame buffering.

The high-level workflow is as follows: when the Raspberry Pi boots, it starts the main Python script. The camera continuously captures frames, which are passed to YOLOv8 to detect persons and vehicle number plates. For each detection, bounding boxes and labels are drawn on the frame, person crops are saved into the persons/ directory, and plate crops are saved into the plates/ directory and processed with OCR to extract text. Meanwhile, the ultrasonic sensor continuously measures distance, and if an obstacle is closer than a defined threshold, the robot stops or changes direction. The processed frames are then served by Flask as an MJPEG stream, which can be viewed from any browser on the same Wi-Fi network by visiting the Pi’s IP address.

A typical repository structure for this project can be organized as follows: a models/ directory containing person.pt and plate.pt; a src/ directory containing main.py for the main control loop (vision, movement, streaming), along with helper modules such as detection.py for YOLOv8 inference, ocr.py for OCR handling, motor_control.py for L298N and movement logic, ultrasonic.py for distance measurement, servo_control.py for camera pan/tilt, and streaming.py for Flask/MJPEG endpoints. The data/ directory can hold persons/ and plates/ subfolders for captured images, while templates/ can contain index.html for the web dashboard. A requirements.txt file lists Python dependencies, and README.md describes the project.

To get started, the user typically clones the repository, copies the code and trained models into the appropriate folders, and installs dependencies with pip install -r requirements.txt. Tesseract OCR must be installed separately on Raspberry Pi OS using the system package manager. After wiring up the L298N, ultrasonic sensor, servo, and camera according to the chosen pin map, the main application can be launched with python src/main.py. On any device connected to the same Wi-Fi network, the user can open a browser, navigate to http://<raspberry-pi-ip>:5000, and view the live stream with detection overlays.

Planned future enhancements include adding cloud backup for captured images and logs, GPS tracking and geo-tagging of events, support for night vision or IR illumination, additional object classes (such as animals, bags, or helmets), and automatic alerting through channels like email, Telegram, or WhatsApp when specific events or objects are detected.

Key Points (for quick view / resume bullets)

Built an autonomous AI surveillance robot using Raspberry Pi 4, Pi Camera, L298N, ultrasonic sensor, and servo-based camera control.

Implemented real-time YOLOv8 detection for persons and Indian vehicle number plates, with automatic image cropping and storage.

Integrated Tesseract OCR to read and log number plate text from detected regions.

Developed a Flask-based live streaming web interface accessible within the same Wi-Fi network for secure monitoring.

Designed a modular Python codebase for vision, motion control, sensing, OCR, and streaming, suitable for extension and future upgrades.

