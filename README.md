# ROS 2 Jetson Speech & Vision Ecosystem

This is a Docker project and ROS 2 workspace (`new_speech_ws`) designed specifically for NVIDIA Jetson edge computing devices. The project combines ROS 2 Humble, PyTorch, various open-source speech recognition/synthesis packages (Whisper, Vosk), and YOLO object detection to provide an out-of-the-box edge AI robot/voice assistant development environment.

## ✨ Features

- **Jetson Optimized Environment:** Based on NVIDIA's official inference image `dustynv/l4t-pytorch:r36.4.0` (with built-in PyTorch).
- **ROS 2 Humble:** Includes the complete ROS 2 Humble Desktop and development toolkits (`ros-dev-tools`).
- **Rich Speech and Audio Packages:** Pre-installed with `openai-whisper`, `vosk`, `speechbrain`, `webrtcvad`, `pyttsx3`, `pyaudio`, `sounddevice`, and pre-configured ALSA and PulseAudio audio transport layers.
- **Computer Vision (YOLO):** Configured `ultralytics` in an isolated Python virtual environment (`yolo_env`) to avoid dependency conflicts.
- **Developer Friendly:**
  - Dedicated non-root user `appuser`.
  - Added to `audio` (microphone/speaker access) and `dialout` (USB Serial access) groups by default.
  - Customized colored Bash prompt (PS1).
  - Automatically sources ROS 2 environment variables.

## 📦 Prerequisites

- NVIDIA Jetson Device (Compatible with L4T r36.4.0 / Jetpack 6.x)
- Docker
- [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html) (Required for GPU access inside the container)

## 🚀 Getting Started

### 1. Build the Docker Image

First, clone this project and build the Docker image using `Dockerfile-Jetson-Pytorch-ROS2`:

```bash
git clone <your-github-repo-url>
cd <your-repo-folder>
docker build -t jetson-ros2-speech -f Dockerfile-Jetson-Pytorch-ROS2 .
```

### 2. Start the Container

To allow the container to access hardware such as the GPU, sound card, and serial ports, and for ease of development, use the following command to start the container:

```bash
docker run --runtime nvidia -it --rm \
  --privileged \
  --network host \
  --device /dev/snd:/dev/snd \
  --device /dev/bus/usb:/dev/bus/usb \
  --group-add audio \
  --group-add dialout \
  --name speech_dev \
  jetson-pytorch-ros2:latest \
  /bin/bash
```

> **Parameter Explanation:**
> - `--runtime nvidia`: Enables GPU support.
> - `--device /dev/snd`: Mounts the host's sound card hardware into the container.
> - `-v $(pwd)/new_speech_ws:/home/appuser/new_speech_ws`: Mounts your local `new_speech_ws` directory into the container, so you can edit code without rebuilding the image.

## 📁 Inside the Container Environment

After logging into the container, the default working directory is `/home/appuser`.

- **ROS 2:** Automatically sourced (`source /opt/ros/humble/setup.bash`).
- **Speech Packages:** All installed in the global system environment via `pip3`.
- **YOLO Virtual Environment:** Located at `~/yolo_env`. Activate it before running vision-related code:
  ```bash
  source ~/yolo_env/bin/activate
  # Type `deactivate` to exit
  ```

## 🛠 Workspace Usage

Inside the container, navigate to the mounted ROS 2 workspace to build and develop:

```bash
cd ~/new_speech_ws
colcon build
source install/setup.bash
```
