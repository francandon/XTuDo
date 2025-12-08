# Installing MESA for This Course (via Docker)

In this course we will **not** compile MESA directly on your laptop. Instead, we will use **MESA-Docker**, which provides:

- A Linux environment,
- A pre-installed MESA version,
- Scripts to start everything with a single command.

This page explains how to set up:

1. Docker,
2. Git and a basic terminal,
3. An X server (for graphical plots),
4. The `MESA-Docker` repository.

Most students will be on **Windows 10/11**, so those instructions are the most detailed, but there are also short sections for **macOS** and **Linux**.

---

## 1. Hardware Requirements

To run MESA-Docker comfortably you should have:

- ≥ 8 GB RAM (16 GB is nicer),
- ≥ 25 GB free disk space:
    - The Docker image itself is ~15 GB when unpacked,
    - Plus space for your models and logs.

If you have less RAM, you may need to limit the number of CPU cores and memory that Docker is allowed to use.

---

## 2. Software Overview

To follow the course you need:

- **Docker**  
  Runs the container that contains MESA.

- **Git**  
  Downloads the `MESA-Docker` scripts and configuration.

- **A terminal**  
    - On Windows: Git Bash,  
    - On macOS: Terminal (or iTerm),
    - On Linux: any terminal (GNOME Terminal, Konsole, etc.).

- **An X server** (for graphical windows from `pgstar`):
    - On Windows: Xming or VcXsrv,
    - On macOS: XQuartz,
    - On Linux: usually already available through your desktop session.

---

## 3. Windows 10 / 11

### 3.1. Install Docker Desktop

1. Download [Docker Desktop](https://www.docker.com/products/docker-desktop/) for Windows.
2. Run the installer, accept defaults.
3. If asked, allow enabling WSL2 and restart the computer.

After installation:

- Start Docker Desktop once and wait until it is running.

### 3.2. Install Git and Git Bash

1. Download [Git](https://desktop.github.com/download/) (or also it should work GitHub Desktop) for Windows. 
2. It will be not only important for the Docker installation but also to clone the seminar repository. 

We will use Git Bash as our terminal on Windows.

### 3.3. Install an X server (Xming or VcXsrv)

MESA’s pgstar plots use the X11 window system. On Windows, you need an X server:

- Download and install [Xming](https://sourceforge.net/projects/xming/)
- Start the X server before starting the MESA container.
- If asked about allowing network connections, allow them.

### 4. Follow the instructions on the MESA Docker Github repository

In order to complete the installation you should follow the instructions at the [GitHub docker website](https://github.com/MESAHub/MESA-Docker). Just scroll down until you see the README. I will list here the main steps. 

## 5. Download the MESA-Docker Repository

Now we get the actual scripts that run the MESA container.

1. Open your terminal:
    - Windows: Git Bash,
    - macOS: Terminal,
    - Linux: your favorite terminal.

2. Choose a location for your MESA work, for example your home directory:
```bash
   cd ~
```
3. Clone the repository:
```bash
   git clone https://github.com/MESAHub/MESA-Docker.git
``` 
4. Move into the new folder:
```bash
   cd MESA-Docker
```

You should now see files like:

- win_dockerMESA.sh
- win_home_dockerMESA.sh
- mac_dockerMESA.sh
- linux_dockerMESA.sh
- and a directory called docker_work (or created when you first run the scripts).

On Windows you should start Docker Desktop and Xming 

```bash
./win_home_dockerMESA.sh -d <drive letter>
```

---

## 6. Quick Checklist Before Running

You are ready to proceed to the next step when:

- Docker Desktop / Docker Engine is installed and starts without errors.
- Git works: git --version prints a version number.
- An X server is installed and can be started:
    - Xming / VcXsrv (Windows),
    - XQuartz (macOS),
    - Desktop environment (Linux).
- You have cloned the MESA-Docker repository and can do:
```bash
  cd /path/to/MESA-Docker
  ls
```

If all that is true, continue to the next page to learn how to start the container and run your first stellar model.
