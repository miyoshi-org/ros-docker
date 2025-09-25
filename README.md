# 🚀 **ROS/NVIDIA Environment Setup with Docker (Latest Supported)**

This repository provides instructions for setting up a **ROS environment on Docker with NVIDIA GPU support**.
It supports **Ubuntu 20.04/22.04** and **Docker Engine 20.10+**.

---

## ✅ **0. Install NVIDIA Container Toolkit**

If you want to use GPU, you need `nvidia-docker`.
**Skip this step if running on CPU only.**

```bash
# Official guide: https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html

# 1. Add the package repositories
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) 
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list \
    | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

# 2. Update package index
sudo apt-get update

# 3. Install nvidia-docker2
sudo apt-get install -y nvidia-docker2

# 4. Restart Docker daemon
sudo systemctl restart docker
```

**Verify GPU inside a container:**

```bash
docker run --rm --gpus all nvidia/cuda:12.2.0-base-ubuntu22.04 nvidia-smi
```

---

## ✅ **1. Add User to Groups**

```bash
sudo usermod -aG dialout $USER    # for serial communication (optional)
sudo usermod -aG docker $USER     # run docker without sudo
```

> ⚠️ After modifying groups, **log out and back in** or reboot to apply.

---

## ✅ **2. Allow GUI Applications (if needed)**

For X11 display forwarding from containers:

```bash
xhost +local:root   # temporarily allow root access
```

Revert afterwards for security:

```bash
xhost -local:root
```

> 💡 **Note for Wayland users:**
> Switch to X11 session or use `x11docker`.

---

## ✅ **3. Clone Repository**

```bash
git clone https://github.com/miyoshi-nii/docker-ros.git

# GPU version:
cd docker-ros/ros-noetic-nvidia  

# CPU-only version:
cd docker-ros/ros-noetic-cpu  

# -------------------------------------------------------------------------
# 💡 Difference between GPU and CPU versions:
# - ros-noetic-nvidia : 
#     Based on NVIDIA CUDA images, requires NVIDIA Container Toolkit.
#     Recommended if you want to use GPU acceleration (e.g. Gazebo rendering,
#     deep learning libraries, hardware acceleration).
#
# - ros-noetic-cpu : 
#     Based on plain Ubuntu images, does not require CUDA or GPU drivers.
#     Suitable for systems without NVIDIA GPU or when only CPU computation is needed.
# -------------------------------------------------------------------------
```

---

## ✅ **4. Build Docker Image**

```bash
docker compose build
```

* If group changes are not applied, use `sudo docker compose build`.
* Use `--no-cache` for a clean build without cache.

---

## ✅ **5. Start Docker Container (Detached Mode)**

```bash
docker compose up -d
```

* `-d` = detached mode (runs in background)
* Check logs with:

  ```bash
  docker compose logs -f
  ```

---

## ✅ **6. Enter the Container**

```bash
docker compose exec miyoshilab bash
```

* `miyoshilab` is the service name defined in `docker-compose.yml`.
* Use `docker compose ps` to check running containers.

---

## ✅ **7. Stop and Remove Containers**

```bash
docker compose down
```

* `down` stops containers and removes networks/volumes.
* Images remain on the system.

---

## 🔍 **💡 Useful Docker Compose Options**

| Option       | Description                  |
| ------------ | ---------------------------- |
| `-d`         | Run in background (detached) |
| `--build`    | Always rebuild before start  |
| `--no-cache` | Build without cache          |
| `logs -f`    | Follow logs in real time     |
| `ps`         | List running containers      |
| `down -v`    | Remove containers + volumes  |

---

## 📌 **Official Documentation**

* [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html)
* [Docker Compose](https://docs.docker.com/compose/)

---

Do you also want me to add a **section for CPU-only setup** (with instructions on how to switch to `ubuntu:20.04` instead of `nvidia/cuda`), or should I keep this README GPU-focused?