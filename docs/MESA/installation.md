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

1. Download Docker Desktop for Windows.
2. Run the installer, accept defaults.
3. If asked, allow enabling WSL2 and restart the computer.

After installation:

- Start Docker Desktop once and wait until it is running.

### 3.2. Install Git and Git Bash

1. Download Git for Windows.
2. During installation, make sure Git Bash is selected.
3. After installation, you will have a program called “Git Bash” in the Start menu.

We will use Git Bash as our terminal on Windows.

### 3.3. Install an X server (Xming or VcXsrv)

MESA’s pgstar plots use the X11 window system. On Windows, you need an X server:

- Download and install Xming or VcXsrv.
- Start the X server before starting the MESA container.
- If asked about allowing network connections, allow them.

---

## 4. macOS

### 4.1. Install XQuartz

1. Download XQuartz.
2. Install it via the .pkg file.
3. Start XQuartz at least once.
4. In XQuartz, go to Preferences → Security and enable  
   “Allow connections from network clients”.  
   Then quit and restart XQuartz.

### 4.2. Install Docker Desktop for Mac

1. Download Docker Desktop for Mac.
2. Drag the Docker icon into /Applications.
3. Start Docker Desktop and keep it running in the background.

### 4.3. Install Git

You have multiple options:

- Install Xcode Command Line Tools (which include Git):

  xcode-select --install

- Or install Git via Homebrew:

  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
  brew install git

---

## 5. Linux

On Linux, the setup is simpler:

1. Install Docker Engine using your distribution’s instructions.
2. Ensure your user can run Docker (often via a docker group).
3. Install git with your package manager, for example:

   sudo apt install git      # Debian/Ubuntu
   sudo dnf install git      # Fedora
   sudo pacman -S git        # Arch

4. Your graphical desktop session usually already provides an X server.

---

## 6. Configure Docker Resources

Docker’s default settings may be sub-optimal for MESA.

In Docker Desktop (Windows/macOS):

1. Open Settings → Resources.
2. Adjust:
   - CPUs: at least 2–4 cores if available.
   - Memory: at least 4–6 GB.
   - Disk image size: enough to store at least 20–30 GB.

On Linux, similar settings can be controlled by Docker’s daemon configuration or via the system’s resource limits, but often the defaults are fine.

---

## 7. Download the MESA-Docker Repository

Now we get the actual scripts that run the MESA container.

1. Open your terminal:
   - Windows: Git Bash,
   - macOS: Terminal,
   - Linux: your favorite terminal.

2. Choose a location for your MESA work, for example your home directory:

   cd ~

3. Clone the repository:

   git clone https://github.com/MESAHub/MESA-Docker.git

4. Move into the new folder:

   cd MESA-Docker

You should now see files like:

- win_dockerMESA.sh
- win_home_dockerMESA.sh
- mac_dockerMESA.sh
- linux_dockerMESA.sh
- and a directory called docker_work (or created when you first run the scripts).

---

## 8. Quick Checklist Before Running

You are ready to proceed to the next step when:

- Docker Desktop / Docker Engine is installed and starts without errors.
- Git works: git --version prints a version number.
- An X server is installed and can be started:
  - Xming / VcXsrv (Windows),
  - XQuartz (macOS),
  - Desktop environment (Linux).
- You have cloned the MESA-Docker repository and can do:

  cd /path/to/MESA-Docker
  ls

If all that is true, continue to the next page to learn how to start the container and run your first stellar model.
