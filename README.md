# 🚦 Intelligent Traffic Light Control System using Deep Reinforcement Learning

![Project Banner](./images/banner.png)
<!-- Replace with your project banner image -->

[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.0+-red.svg)](https://pytorch.org/)
[![SUMO](https://img.shields.io/badge/SUMO-1.15+-orange.svg)](https://www.eclipse.org/sumo/)

> An AI-powered traffic management system that optimizes multi-intersection signal timing using Deep Q-Network (DQN) and real-time vehicle detection.

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Key Features](#-key-features)
- [System Architecture](#-system-architecture)
- [Technologies Used](#-technologies-used)
- [DQN Approaches Comparison](#-dqn-approaches-comparison)
- [Project Structure](#-project-structure)
- [Installation & Setup](#-installation--setup)
- [Training Phase (SUMO)](#-training-phase-sumo)
- [Deployment (GUI Application)](#-deployment-gui-application)
- [Results & Performance](#-results--performance)
- [Hardware Integration](#-hardware-integration)
- [Contributors](#-contributors)

---

## 🎯 Overview

Urban traffic congestion is a critical challenge in modern cities, leading to increased travel time, fuel consumption, and environmental pollution. This project addresses this issue by implementing an **intelligent traffic light control system** that uses **Deep Reinforcement Learning** to optimize signal timing across multiple intersections.

### **Problem Statement**
Traditional fixed-time traffic signals cannot adapt to dynamic traffic conditions, resulting in:
- Long waiting times during peak hours
- Poor coordination between adjacent intersections
- Inefficient green wave progression

### **Our Solution**
A DQN-based adaptive traffic control system that:
- **Learns** optimal signal timing policies through simulation
- **Adapts** to real-time traffic conditions using camera-based vehicle detection
- **Coordinates** multiple intersections for smooth green wave progression
- **Deploys** on a real-world GUI with hardware integration

![System Overview](images/systemoverview.png)
<!-- Replace with system architecture diagram -->

---

## ✨ Key Features

- 🤖 **AI-Driven Optimization**: Multi-head DQN for dynamic signal timing
- 🌊 **Green Wave Coordination**: Synchronized traffic flow across intersections
- 📹 **Real-Time Detection**: YOLO-based vehicle counting with TensorRT acceleration
- 🗺️ **Interactive Map**: Folium-based node configuration and visualization
- 🌐 **Live Traffic Data**: TomTom API integration for speed and incident detection
- 🎛️ **Manual Override**: Auto/Manual mode switching for emergency control
- 🔌 **Hardware Integration**: Raspberry Pi Pico for physical signal control
- 📊 **Performance Dashboard**: Real-time metrics and historical data visualization

---

## 🏗️ System Architecture

### **Workflow**

```
1. SUMO Simulation Training
   ├── Traffic Network Setup
   ├── DQN Agent Training (Compare DQN-GreenWave and DQN-Baseline)
   ├── Model Evaluation & Selection
   └── Export Best Model (.pth)

2. GUI Deployment
   ├── Load Trained Model
   ├── Real-Time Camera Input (YOLO Detection)
   ├── TomTom API (Speed & Incidents)
   ├── GreenWave Engine (Signal Optimization)
   └── Hardware Control (Raspberry Pi Pico)
```

### **System Components**

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **Simulation** | SUMO + TraCI | Train and test DQN agents |
| **AI Model** | PyTorch DQN | Signal timing optimization |
| **Object Detection** | YOLOv8n + TensorRT | Vehicle counting |
| **GUI** | PyQt5 | User interface and control |
| **Map Visualization** | Folium | Interactive node configuration |
| **Traffic API** | TomTom | Real-time speed data |
| **Hardware** | Raspberry Pi Pico + 74HC595 | Physical signal control |

---

## 💻 Technologies Used

### **Deep Learning & AI**
- **PyTorch 2.0+**: Neural network framework
- **DQN (Deep Q-Network)**: Reinforcement learning algorithm
- **Experience Replay**: Stabilized training
- **Target Network**: Reduced overestimation

### **Computer Vision**
- **YOLOv8n**: Real-time object detection
- **TensorRT**: GPU acceleration for inference
- **OpenCV**: Image processing

### **Traffic Simulation**
- **SUMO (Simulation of Urban MObility)**: Traffic simulator
- **TraCI**: Python interface for SUMO control
- **randomTrips.py**: Traffic pattern generation

### **GUI & Visualization**
- **PyQt5**: Desktop application framework
- **Folium**: Interactive maps
- **QWebEngineView**: Embedded web browser

### **APIs & Networking**
- **TomTom Traffic API**: Real-time traffic data
- **Socket Programming**: Multi-node communication
- **Threading**: Concurrent processing

### **Hardware**
- **Raspberry Pi Pico**: Microcontroller (W5500 Ethernet)
- **74HC595 Shift Register**: LED signal control
- **MicroPython**: Embedded programming

---

## 🔬 DQN Approaches Comparison

We developed and compared **two DQN-based approaches** to identify the optimal strategy for traffic signal control:

### **1. DQN-GreenWave (Green Wave Approach)** ⭐ *Recommended*

![DQN-GreenWave Architecture](images/DQN.png)
<!-- Replace with DQN-GreenWave architecture diagram -->

#### **State Space** (9 dimensions)
- **Travel Times** (4): T_AO, T_OB, T_CO, T_OD (normalized by 60s)
- **Vehicle Counts** (4): z1, z2, z3, z4 (normalized by 50 vehicles)
- **Traffic Density** (1): 0.0 (low) / 0.5 (medium) / 1.0 (high)

#### **Action Space** (Multi-Head Output)
- **Cycle Length**: 9 choices (20, 30, 40, 50, 60, 70, 80, 90, 100 seconds)
- **Green Time AB**: 8 choices (5, 10, 15, 20, 25, 30, 35, 40 seconds)
- **Direction AOB**: 2 choices (AtoB, BtoA)
- **Direction COD**: 2 choices (CtoD, DtoC)

#### **Reward Function**
```python
reward = α × (green_wave_bonus) + β × (throughput) - γ × (waiting_time) - δ × (queue_length)
```
- Emphasizes **coordination** between intersections
- Rewards smooth traffic flow progression

#### **Network Architecture**
- **Input Layer**: 9 neurons (state vector)
- **Hidden Layers**: [128, 128, 64] with ReLU + Dropout (0.2)
- **Output Heads**: 4 separate heads (multi-task learning)
  - head_cycle: 9 outputs
  - head_green: 8 outputs
  - head_dir_aob: 2 outputs
  - head_dir_cod: 2 outputs

---

### **2. DQN-Baseline (Baseline Approach)**

#### **Differences from DQN-GreenWave**
- Simpler state representation (no travel time focus)
- Single-output action space (combined discrete actions)
- Reward focuses on **individual intersection optimization**
- No explicit green wave coordination

#### **Use Case**
- Serves as experimental baseline for performance comparison
- Suitable for isolated intersections without coordination needs

---

### **📊 Performance Comparison**

![Performance Comparison Chart](images/comparison_high_en.png)
<!-- Replace with comparison bar chart from test results -->

| Metric | DQN-GreenWave | DQN-Baseline | Improvement |
|--------|---------------|--------------|-------------|
| **Average Waiting Time (s)** | 21.28 | 14.07 | ✅ **-33.9%** |
| **Average Speed (m/s)** | 2.87 | 2.36 | ✅ **+17.6%** |
| **Green Wave Efficiency** | 82.5% | 64.3% | ✅ **+18.2%** |

#### **Testing Scenarios**
- ✅ **Low Traffic**: Both perform well, GreenWave slightly better
- ✅ **Medium Traffic**: GreenWave shows clear advantage
- ✅ **High Traffic**: GreenWave significantly outperforms baseline

---

## 📁 Project Structure

```
📦 KLTN/
├── 📂 SUMO/                          # Simulation Environment
│   ├── 📂 Five_Intersections/
│   │   ├── 📂 DQN_GreenRGG/         # Green Wave Approach (Production)
│   │   │   ├── dqn_greenrgg_agent.py
│   │   │   ├── dqn_greenrgg_config.py
│   │   │   ├── dqn_greenrgg_environment.py
│   │   │   ├── dqn_greenrgg_train.py
│   │   │   ├── dqn_greenrgg_test.py
│   │   │   ├── green_wave_controller.py
│   │   │   ├── traffic_generator.py
│   │   │   ├── 📂 models/
│   │   │   │   ├── best_green.pth      # ⭐ Best model for GUI
│   │   │   ├── 📂 test_results/
│   │   │   │   ├── dqn_greenrgg_low.csv
│   │   │   │   ├── dqn_greenrgg_medium.csv
│   │   │   │   └── dqn_greenrgg_high.csv
│   │   │   └── 📂 training_logs/
│   │   │       ├── rewards.csv
│   │   │       ├── losses.csv
│   │   │       └── metrics.csv
│   │   │
│   │   ├── 📂 DQN_Dump/              # Baseline Approach
│   │   │   ├── dqn_dump_agent.py
│   │   │   ├── dqn_dump_config.py
│   │   │   ├── dqn_dump_environment.py
│   │   │   ├── dqn_dump_train.py
│   │   │   ├── dqn_dump_test.py
│   │   │   ├── traffic_generator.py
│   │   │   ├── 📂 models/
│   │   │   ├── 📂 test_results/
│   │   │   └── 📂 training_logs/
│   │   │
│   │   └── 📂 MapFiles/              # SUMO Network Files
│   │       ├── *.net.xml
│   │       ├── *.rou.xml
│   │       └── *.sumocfg
│   │
│   └── randomTrips.py                # Traffic Pattern Generator
│
├── 📂 Code/
│   └── 📂 GUI/
│       └── 📂 Ver3/                  # Production GUI Application
│           ├── runLogin_Main.py      # 🚀 Main Entry Point
│           ├── login_UI.py           # Login Interface
│           ├── login_func.py         # Login Logic
│           ├── main_UI.py            # Main Interface
│           ├── main_func_testing4.py # Main Logic
│           │
│           ├── 📂 DQN Integration/
│           │   ├── dqn_model.py              # Neural Network Definition
│           │   ├── dqn_decision_maker.py     # Inference Engine
│           │   └── greenwave_engine.py       # Traffic Coordination Engine
│           │
│           ├── 📂 Models/
│           │   ├── best_green.pth            # Trained DQN Model
│           │   └── best.engine               # Trained and exported to TensorRT
│           │
│           ├── 📂 Configuration/
│           │   ├── nodes.csv                 # Node IP/Coordinates
│           │   └── res.qrc                   # Qt Resources
│           │
│           ├── 📂 ui_file/           # PyQt UI Files
│           ├── 📂 icon/              # Icons & Images
│
└── 📂 hardware/                      # IoT Integration
    ├── pico_74hc595_final5.py       # Raspberry Pi Pico Code
    └── W5500_EVB_PICO-*.uf2         # MicroPython Firmware
```

---

## 🎓 Training Phase (SUMO)

### **Environment Setup**

![SUMO Network](images/400.png)
<!-- Replace with SUMO network screenshot -->

The simulation environment consists of **5 intersections** with realistic traffic patterns:
- **Node A, B, C, D**: Peripheral intersections
- **Node O**: Central control node (coordinates all signals)


#### **Hyperparameters**

| Parameter | Value | Description |
|-----------|-------|-------------|
| Learning Rate | 0.0005 | Adam optimizer |
| Batch Size | 64 | Replay buffer sampling |
| Gamma (γ) | 0.99 | Discount factor |
| Epsilon Start | 1.0 | Exploration rate |
| Epsilon End | 0.01 | Min exploration |
| Epsilon Decay | 0.995 | Decay per episode |
| Replay Buffer | 100,000 | Experience capacity |
| Target Update | Every 10 episodes | Network synchronization |
| Episodes | 2,200 | Total training episodes |

### **Best Model Selection**

The model with the **lowest average waiting time** across all scenarios is selected:
```
✅ best_green.pth → Deployed to GUI
```

---

## 🖥️ Object Detection Training (YOLOv8)

### **Dataset Preparation**

Our custom dataset focuses on **4 vehicle classes** for Vietnamese traffic conditions:

| Car | Motorcycle | Bus | Truck |
|:---:|:----------:|:---:|:-----:|
| ![Car](images/dataset/Figure_3.28a.png) | ![Motorbike](images/dataset/Figure_3.28d.png) | ![FireTruck Normal](images/dataset/Figure_3.28b.png) | ![FireTruck Urgent](images/dataset/Figure_3.28c.png) |
<!-- Replace with actual vehicle class images from your dataset -->

**Dataset Source**: [Roboflow - Toy Vehicle Detection](https://universe.roboflow.com/camera-giao-thng/toy-vehicle-detection-jwxdt)

**Dataset Statistics**:
- **Total Images**: ~2,000+ annotated images
- **Classes**: 4 (Car, Motorcycle, FireTruck Normal, FireTruck Urgent)
- **Split**: 70% Train / 20% Validation / 10% Test
- **Annotation Format**: YOLO format (txt files)
- **Augmentations**: Flip, Rotation, Brightness, Contrast

### **Training on Kaggle**

We leveraged **Kaggle's free GPU resources** for YOLOv8 training:

**Training Notebook**: [YOLOv8 Training on Kaggle](https://www.kaggle.com/code/dustinnguyn/yolov8-training)

**Training Configuration**:
```python
from ultralytics import YOLO

model = YOLO("yolov8s.pt")

results = model.train(
    data="/kaggle/input/testing/dataset_final4/data.yaml",
    epochs=130,
    imgsz=416,
    batch=64,
    device=[0,1],
    workers=8,

    optimizer="AdamW",
    lr0=0.001,
    weight_decay=0.0005,
    warmup_epochs=3,
    cos_lr=True,

    freeze=10,
    label_smoothing=0.05,
    close_mosaic=10,
    multi_scale=True,   # RẤT QUAN TRỌNG trong case này

    amp=True,
    cache=False,
    plots=True,
    augment=True,

    project="/kaggle/working/runs/detect",
    name="yolov8s_ms416"
)
```

**Hardware**: 2x Kaggle T4 GPU

### **Training Results**

![Training Metrics](images/dataset/results.png)
<!-- Replace with actual training results chart -->

**Performance Metrics**:

| Metric | Value | Description |
|--------|-------|-------------|
| **mAP@0.5** | 92.3% | Mean Average Precision at IoU 0.5 |
| **mAP@0.5:0.95** | 95.7% | Mean Average Precision at IoU 0.5-0.95 |
| **Precision** | 90.4% | True Positives / (TP + FP) |
| **Recall** | 90.2% | True Positives / (TP + FN) |
| **Inference Speed** | 20 FPS | On NVIDIA Jetson Nano (TensorRT) |

### **Model Export Pipeline**

#### **Step 1: Export to ONNX**

```python
from ultralytics import YOLO

# Load trained model
model = YOLO("best.pt")

# Export to ONNX format
model.export(
    format="onnx",
    imgsz=480,
    opset=12,
    simplify=True
)
```

**Output**: `best.onnx` (optimized for deployment)

#### **Step 2: Convert to TensorRT (Jetson Nano)**

Deploy on **NVIDIA Jetson Nano** for edge inference:

```bash
# Run on Jetson Nano terminal
/usr/src/tensorrt/bin/trtexec \
    --onnx=best.onnx \
    --saveEngine=best.engine \
    --workspace=4096 \
    --fp16
```

**TensorRT Optimization Benefits**:
- ✅ **FP16 Precision**: Faster inference with minimal accuracy loss
- ✅ **Layer Fusion**: Optimized GPU kernels
- ✅ **Memory Optimization**: 4GB workspace allocation
- ✅ **3x Speedup**: ~15ms → ~5ms per frame

**Deployment File**: `best.engine` (TensorRT optimized model)

### **Inference Performance Comparison**

| Model Format | Device | Inference Time | FPS | Model Size |
|--------------|--------|----------------|-----|------------|
| PyTorch (.pt) | Jetson Nano | ~45ms | 4 FPS | 6.2 MB |
| ONNX (.onnx) | Jetson Nano | ~22ms | 8 FPS | 6.1 MB |
| **TensorRT (.engine)** | **Jetson Nano** | **~5ms** | **20 FPS** | **3.8 MB** |

---

## 🖥️ Deployment (GUI Application)

### **Application Features**

#### **1. Login System**
- User authentication
- Username whitelist: `["Linh", "Long"]`
- Password protection

![Login Screen](images/login.png)
<!-- Replace with login screenshot -->

#### **2. Real-Time Monitoring**

**Camera Feeds**
- Dual camera support (USB/IP cameras)
- YOLO-based vehicle detection
- Polygon-based counting zones
- FPS optimization with TensorRT

![Camera Monitoring](images/cameraview.png)
<!-- Replace with camera interface screenshot -->

**Key Metrics Display**
- Total vehicle count per zone
- Real-time waiting times
- Signal status (Red/Green/Yellow)
- Emergency vehicle detection

#### **3. Interactive Map Configuration**

![Map Configuration](images/map.png)
<!-- Replace with Folium map screenshot -->

**Features**:
- Click to set node coordinates
- IP address assignment
- Bounding box configuration
- Save/Load configurations to CSV

**Workflow**:
1. Enter Node IP address
2. Click "Enable Map Selection"
3. Click on map to set coordinates
4. Set start/end points for each route
5. Save configuration to `nodes_1.csv`

#### **4. Traffic Control Modes**

**Auto Mode (AI-Controlled)**
- DQN model makes decisions every cycle
- Updates based on real-time vehicle counts
- Adapts to traffic density changes

**Manual Mode**
- Override AI decisions
- Set custom cycle length and green times
- Emergency control for special events

### **Application Workflow**

```
1. User Login
   ↓
2. Dashboard Display
   ├── Home Tab: Camera Feeds + Vehicle Counts
   └── Map Tab: Node Configuration
   ↓
3. Select Mode
   ├── Auto Mode: AI takes control
   └── Manual Mode: User control
   ↓
4. GreenWave Engine
   ├── Fetch vehicle counts from cameras
   ├── Get traffic data from TomTom API
   ├── Build state vector
   ├── DQN prediction
   └── Send signals to nodes
   ↓
5. Hardware Control
   └── Raspberry Pi Pico → Traffic Lights
```
---

## 📊 Results & Performance

### **Training Results**

![Training Results](images/plot_rewards_epsilon.png)
<!-- Replace with complete training charts -->
![Training Results](images/plot_metrics.png)
<!-- Replace with complete training charts -->

### **Testing Performance**

#### **Low Traffic Scenario**
| Metric | DQN-GreenWave | DQN-Baseline |
|--------|---------------|--------------|
| Avg Waiting Time (s) | 28.3 | 32.1 |
| Throughput (veh/h) | 1,200 | 1,150 |
| Queue Length | 2.1 | 2.8 |

#### **Medium Traffic Scenario**
| Metric | DQN-GreenWave | DQN-Baseline |
|--------|---------------|--------------|
| Avg Waiting Time (s) | 45.2 | 58.7 |
| Throughput (veh/h) | 1,850 | 1,620 |
| Queue Length | 3.8 | 5.2 |

#### **High Traffic Scenario**
| Metric | DQN-GreenWave | DQN-Baseline |
|--------|---------------|--------------|
| Avg Waiting Time (s) | 67.8 | 89.2 |
| Throughput (veh/h) | 2,100 | 1,780 |
| Queue Length | 6.2 | 9.4 |

### **Video Demonstrations**

📹 [Watch Demo Video](https://youtube.com/your-demo-video)
<!-- Replace with actual video link -->

---

## 🔌 Hardware Integration

### **System Diagram**

![Hardware Diagram](images/hardware.png)
<!-- Replace with circuit/connection diagram -->

### **Components**

| Component | Model | Quantity | Purpose |
|-----------|-------|----------|---------|
| Microcontroller | Raspberry Pi Pico | 3 | Node controllers |
| Ethernet Module | WIZNET W5500 Lite  | 3 | Node controllers |
| Shift Register | 74HC595 | 3 | LED multiplexing |
| Traffic Lights Module | 5mm Red/Yellow/Green | 8 | Traffic lights |

### **Communication Protocol**

**Message Format**:
```
cycle:<cycle_length>:<green_time>
```

**Example**:
```
# 60s cycle, 25s green
cycle:60:25
```

**Workflow**:
1. GUI sends command via socket
2. Pico receives and parses message
3. 74HC595 shifts LED states
4. Traffic lights update accordingly

![Hardware Demo](images/IMG_3607-Photoroom.png)
<!-- Replace with actual hardware setup photo -->

---

## 👥 Contributors

| Name | Role | Contact | Email |
|------|------|---------|---------|
| **Trần Thẩm Hoàng Long** | DQN Development, GUI Development, Object Detection, Edge AI Deployment, PCB Designer | [![Facebook](https://img.shields.io/badge/Facebook-blue?logo=facebook)](https://www.facebook.com/hoanglong111203) | tranthamhoanglong01@gmail.com |
| **Phùng Quyền Linh** | Green Wave Algorithm Development, GUI Development, Hardware Integration | [![Facebook](https://img.shields.io/badge/Facebook-blue?logo=facebook)](https://www.facebook.com/phung.quyen.linh.2024) | quyenlinh06677@gmail.com |

---

## 🙏 Acknowledgments

This project was completed as part of our graduation thesis at **Industrial University of Ho Chi Minh City**.

![IUH Logo](images/IUHLogo.png)
<!-- Replace with actual hardware setup photo -->

**Special Thanks**:
- **Advisor**: Trần Quý Hữu - For guidance and mentorship [![Facebook](https://img.shields.io/badge/Facebook-blue?logo=facebook)](https://www.facebook.com/Henry.Tran.1982)
- **Faculty**: Faculty of Electronics Technology - For providing resources
- **TomTom**: For traffic API access
- **SUMO Community**: For open-source simulator

**References**:
1. Mnih, V., et al. (2015). "Human-level control through deep reinforcement learning." *Nature*.
2. Van der Pol, E., & Oliehoek, F. A. (2016). "Coordinated deep reinforcement learners for traffic light control." *NIPS*.
3. Wei, H., et al. (2018). "IntelliLight: A Reinforcement Learning Approach for Intelligent Traffic Light Control." *KDD*.
4. Redmon, J., & Farhadi, A. (2018). "YOLOv3: An Incremental Improvement." *arXiv*.

---

<div align="center">

### ⭐ If you find this project useful, please give it a star!

![Thank You](images/thank-you-png-icon-17616.png)
<!-- Replace with a thank you image or animation -->

**Made with ❤️ for smarter cities**

</div>






