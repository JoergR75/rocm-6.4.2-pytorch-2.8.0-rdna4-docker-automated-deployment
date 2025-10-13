# ROCm 6.4.2 + OpenCL 2.x + PyTorch 2.8.0 (Stable) + Transformers 4.x + Docker 28.4 Setup

[![ROCm](https://img.shields.io/badge/ROCm-6.4.2-ff6b6b?logo=amd)](https://rocm.docs.amd.com/en/docs-6.4.2/about/release-notes.html)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.8.0%2Brocm6.4-ee4c2c?logo=pytorch)](https://pytorch.org/blog/pytorch-2-8/)
[![Docker](https://img.shields.io/badge/Docker-28.4.0-blue?logo=docker)](https://www.docker.com/)
[![Ubuntu](https://img.shields.io/badge/Ubuntu-22.04%20%7C%2024.04-e95420?logo=ubuntu)](https://ubuntu.com)

## 📌 Overview
This repository provides an **automated installation script** for setting up a complete **AMD ROCm 6.4.3** development environment with:
- **ROCm 6.4.2** GPU drivers + OpenCL 2.x SDK  
- **PyTorch 2.8.0 (Stable)** ROCm build  
- **Transformers 4.57.0** + **Accelerate + Diffusers + Datasets**  
- **Docker environment** with AMD GPU support  
- **Preconfigured GPU test scripts**

The setup is fully **non-interactive** and optimized for both **desktop** and **server** deployments.

---

## 🖥️ Supported Platforms

| **Component**      | **Supported Versions**                                |
|---------------------|------------------------------------------------------|
| **OS**            | Ubuntu 22.04.x (Jammy Jellyfish), Ubuntu 24.04.x (Noble Numbat) |
| **Kernel**        | 5.15.x (22.04) • 6.8.x (24.04)                        |
| **GPUs**          | AMD CDNA2 • CDNA3 • **RDNA3** • **RDNA4**                 |
| **Docker**        | 28.5.1 (stable)                                       |
| **ROCm**          | 6.4.2                                                |
| **PyTorch**       | 2.8.0                            |
| **Transformers**  | 4.57.0                                               |

> **⚠️ Note**: **Ubuntu 20.04.x (Focal Fossa)** is **not supported**. The last compatible ROCm version for 20.04 is **6.4.0**.

---

## ⚡ Features
- Automated **ROCm GPU drivers + HIP + OpenCL SDK** installation
- **PyTorch ROCm nightly** with GPU acceleration
- Preinstalled **Transformers**, **Accelerate**, **Diffusers**, and **Datasets**
- Integrated **Docker environment** with ROCm GPU passthrough
- **vLLM Docker images** for **RDNA4** & **CDNA3**
- Optimized for **AI workloads**, **LLM inference**, and **model fine-tuning**

---

## 🚀 Installation

### 1️⃣ **Download the Script from the Repository**
```bash
wget https://raw.githubusercontent.com/JoergR75/rocm-6.4.2-pytorch-2.8.0-rdna4-docker-automated-deployment/refs/heads/main/script_module_ROCm_642_Ubuntu_22.04-24.04_pytorch_280stable_server.py
```

<img width="920" height="228" alt="{F825B0F7-4B09-4BF5-B946-BB2D54C338F1}" src="https://github.com/user-attachments/assets/be349829-e148-4e7a-a629-0549b7532220" />

### 2️⃣ **Run the Installer**
```bash
bash script_module_ROCm_642_Ubuntu_22.04-24.04_pytorch_280stable_server.py
```

<img width="933" height="229" alt="{3FEC3863-B012-4D76-B8FA-D9CB4554286D}" src="https://github.com/user-attachments/assets/01611863-6956-4d6a-9a7d-c7820e1a1a94" />

The installation takes ~15 minutes depending on internet speed and hardware performance.

## 🐋 Docker Integration

The script sets up a Docker environment with GPU passthrough support via ROCm.

Check Docker Installation
```bash
docker --version
```
<img width="336" height="51" alt="{F2D3E67E-82FF-4B89-9716-D30432F7BA02}" src="https://github.com/user-attachments/assets/9149968c-ff76-46d4-a821-d43ab20baf43" />

### 🤖 vLLM Docker Images

To use vLLM optimized for RDNA4 and CDNA3:
Use the container image you need.
```bash
# RDNA4 build
sudo docker pull rocm/vllm-dev:open-r9700-08052025

# CDNA3 build
sudo docker pull rocm/vllm:latest
```

Run vLLM with all available AMD GPU Access (example for RDNA4)
```bash
sudo docker run -it \
    --device=/dev/kfd \
    --device=/dev/dri \
    --security-opt seccomp=unconfined \
    --group-add video \
    rocm/vllm-dev:open-r9700-08052025
```
With "rocm-smi" you can verify all available GPUs (here 2x Radeon AI PRO R9700 GPUs)
<img width="929" height="199" alt="{F715178C-A958-4529-9BB3-9F2E2F7661A2}" src="https://github.com/user-attachments/assets/46094f88-5540-453d-829e-f2ec07b3ad95" />

If you need to add a specific GPU, you can use the **passthrough** option.  
First, verify the available GPUs in the `/dev/dri` directory.

<img width="381" height="64" alt="{CA7F5FFD-B028-4620-B625-A0FCDA00155D}" src="https://github.com/user-attachments/assets/b976b314-f885-4373-8452-be52a8a05244" />

Let's choose **GPU2**, also referred to as **"card2"** or **"renderD129"**.
```bash
sudo docker run -it \
    --device=/dev/kfd \
    --device=/dev/dri/card2 \
    --device=/dev/dri/renderD129 \
    --security-opt seccomp=unconfined \
    --group-add video \
    rocm/vllm-dev:open-r9700-08052025
```
GPU2 has been add to the container
<img width="933" height="305" alt="{988A3311-56B1-4BDB-95A6-DF00A4D2BE6D}" src="https://github.com/user-attachments/assets/b630ad80-b163-453a-be29-b03c346aae8b" />

## 🧪 Testing ROCm + PyTorch

After rebooting, verify your setup:

This script creates a simple diagnostic python file (test.py) to verify that PyTorch with ROCm support is correctly installed and working.

What it does:

- Prints the PyTorch version and ROCm version.
- Checks if ROCm is available and how many GPUs are detected.
- Displays the name of the first GPU (if available).
- Creates two random 3×3 tensors directly on the GPU (if available).
- Performs a simple tensor addition operation on the GPU.
- Prints confirmation that the operation was successful and shows the result.

Example usage:
```bash
python3 test.py
```
Expected Output Example:

<img width="428" height="170" alt="{4EC9DFE2-F166-4C51-9111-B75EFD43EFC0}" src="https://github.com/user-attachments/assets/9aa47f7c-7e63-4c43-813b-2d5bd00e7fff" />

## 📶 ROCm Bandwidth Test (`rocm-bandwidth-test -a`)

**AMD’s ROCm Bandwidth Test utility** with the **`-a` (all tests)** flag runs a complete set of bandwidth diagnostics.

### What it does

`rocm-bandwidth-test` is a diagnostic tool included in ROCm that measures **memory bandwidth performance** between:

- Host (CPU) ↔ GPU(s)  
- GPU ↔ GPU (if multiple GPUs are installed)  
- GPU internal memory  

### `-a` (all tests) option

Using the `-a` flag runs **all available tests**, including:

- **Host-to-Device (H2D)** bandwidth  
- **Device-to-Host (D2H)** bandwidth  
- **Device-to-Device (D2D)** bandwidth (for multi-GPU)  
- **Bidirectional / concurrent** bandwidth tests  

Run the P2P test
```bash
sudo /opt/rocm/bin/rocm-bandwidth-test -a
```

### Output

The tool prints results in a **matrix format** showing bandwidth (GB/s) between every device pair.

<img width="646" height="663" alt="{51926F23-C527-4447-89E4-69A64A4CB02C}" src="https://github.com/user-attachments/assets/1799223f-a123-41e9-9f87-d4ddf5f9266a" />

⚠️ **Caution:**  
Make sure **"Resize BAR"** is enabled in the **SBIOS**.  
If it is disabled, **P2P** will be deactivated, as shown below:

<img width="458" height="520" alt="image" src="https://github.com/user-attachments/assets/31f929b0-0c6a-4f3e-b2e7-e2dd4ac60ef7" />

### How to Enable **Resize BAR** in SBIOS

1. Enter **SBIOS**  
2. Navigate to **Advanced**  
3. Go to **PCI Subsystem Settings**

<img width="357" height="203" alt="image" src="https://github.com/user-attachments/assets/0f1d7c5f-5433-4c5e-afd8-72158c603482" />
<img width="492" height="150" alt="image" src="https://github.com/user-attachments/assets/4261936a-d983-481a-8129-9f9bd1f8a0a4" />
