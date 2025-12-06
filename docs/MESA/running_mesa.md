# Running MESA Inside the Docker Container

Once Docker and MESA-Docker are installed, you can actually run MESA.

This page explains:

1. How to start the MESA-Docker container on each OS,
2. What the docker_work directory is,
3. How to run a first test model,
4. How to edit input files (inlists),
5. How to stop and restart the container.

---

## 1. Before Each MESA Session

Every time you want to work with MESA, do this first.

### Windows

1. Start Docker Desktop and wait until it is running.
2. Start your X server (Xming or VcXsrv).
3. Open Git Bash and go to your MESA-Docker folder, for example:

   cd /c/Users/<your-username>/MESA-Docker

### macOS

1. Start Docker Desktop.
2. Start XQuartz.
3. Open Terminal and go to your MESA-Docker folder, for example:

   cd ~/MESA-Docker

### Linux

1. Ensure Docker is running (for example, systemctl status docker).
2. Open a terminal in your usual graphical session and go to your MESA-Docker folder:

   cd ~/MESA-Docker

Adjust the paths if you cloned MESA-Docker somewhere else.

---

## 2. Starting the MESA-Docker Container

In the MESA-Docker folder you will find scripts to start the container. Use the one matching your OS.

### 2.1. macOS

./mac_dockerMESA.sh

On the first run:

- Docker will download the MESA image (several GB).
- SSH will warn about an unknown host. Type "yes" to continue.
- When prompted for a password, enter:

mesa

If everything works, your prompt will change to something like:

docker@mesa-container:~$

This means you are now inside the container.

---

### 2.2. Linux

sudo ./linux_dockerMESA.sh

On the first run:

- Docker downloads and unpacks the MESA image.
- SSH may ask you to confirm authenticity (type "yes").
- Enter password mesa for the docker user.

You should then see:

docker@mesa-container:~$

---

### 2.3. Windows 10 / 11 (with Docker Desktop)

In Git Bash, inside MESA-Docker:

./win_dockerMESA.sh

On the first run:

- Docker downloads the image (this may take a while).
- SSH will ask you to confirm the unknown host → type "yes".
- When asked for a password, enter:

mesa

You might also see a prompt asking Docker for permission to access your hard drive. Allow it.

Once you see:

docker@mesa-container:~$

you are inside the container.

Note for Windows Home / older systems:
Some older systems may need ./win_home_dockerMESA.sh instead, which sets up a virtual machine plus the Docker container. If you ever use this script, it may ask for two passwords the first time (tcuser for the VM and mesa for the container). For this course we will assume most students can use ./win_dockerMESA.sh.

---

## 3. The docker_work Directory (Your Workspace)

Inside the container, run:

pwd
ls

You should be in /home/docker. One of the listed directories will be:

docker_work

This directory is shared with your host computer:

- Inside container: /home/docker/docker_work
- On your host OS: MESA-Docker/docker_work

Anything you save inside ~/docker_work:

- Persists even if you delete the container,
- Is visible on your actual computer (so you can edit with your preferred editor).

Example mapping:

- /home/docker/docker_work/tutorial  ↔  MESA-Docker/docker_work/tutorial
- /home/docker/docker_work/tutorial/LOGS  ↔  MESA-Docker/docker_work/tutorial/LOGS

You will do all your course work inside this shared directory.

---

## 4. Your First MESA Run

Let’s verify that everything works by running a standard test project.

Inside the container:

cd ~/docker_work
cp -r $MESA_DIR/star/work tutorial
cd tutorial
./mk
./rn

What this does:

- cp -r $MESA_DIR/star/work tutorial  
  Copies a standard "work" example into a new folder tutorial in your workspace.

- ./mk  
  Compiles the MESA code for this particular project. The first compilation can take a bit longer.

- ./rn  
  Runs the stellar evolution model and, if your X server is working, opens graphical pgstar windows showing live plots.

You should see at least one window pop up (HR diagram, profiles, etc.) and update as the model evolves.

If you do not see any windows:

- Make sure XQuartz (macOS) or Xming/VcXsrv (Windows) is running.
- Check that firewall / permissions allow network connections from Docker to your X server.
- Try closing XQuartz/Xming completely and starting it again before re-running ./rn.

---

## 5. Inspecting Output

Once the run finishes (or even while it is running), inspect the contents of tutorial:

ls

You should see files such as:

- inlist_project — the main input file controlling this run.
- LOGS/ — directory with output logs:
  - history.data — time series of global quantities (L, R, Teff, etc.).
  - profileNNN.data — internal structure at specific times or stages.
- Possibly other directories created by pgstar (images, etc.).

Because tutorial is inside ~/docker_work, you can open these files from your host OS:

- Windows example path:  
  C:\Users\<you>\MESA-Docker\docker_work\tutorial\LOGS\history.data
- macOS/Linux example path:  
  ~/MESA-Docker/docker_work/tutorial/LOGS/history.data

You can use any editor or plotting tool (e.g. Python, MATLAB, TOPCAT) to analyze them.

---

## 6. Editing the Inlist

The main way you control a MESA run is through the inlist, here:

tutorial/inlist_project

Typical workflow:

1. Edit inlist_project on your host OS  
   - Open MESA-Docker/docker_work/tutorial/inlist_project in a text editor.
   - Change a parameter (for example, the initial mass).

2. Re-run the model inside the container

   cd ~/docker_work/tutorial
   ./rn

You generally only need to re-run ./mk if you modify the Fortran source code. For changes in the inlist, ./rn is enough.

By repeating this cycle:

1. Edit inlist_project,
2. Run ./rn,
3. Look at LOGS/history.data and the pgstar plots,

you build intuition for how different physical assumptions affect stellar evolution.

---

## 7. Stopping and Restarting the Container

When you’re done with your session:

1. Close any pgstar windows (if needed).
2. In the container shell, type:

   exit

This ends the shell session and stops the container instance.

Important:

- All files in ~/docker_work remain on your machine.
- Next time you want to work, just:
  - Start Docker + X server (if needed),
  - Run the appropriate script (./win_dockerMESA.sh, ./mac_dockerMESA.sh, or sudo ./linux_dockerMESA.sh),
  - Go back to ~/docker_work inside the container.

You can even restart from a saved model if the example provides restart options, for example:

cd ~/docker_work/tutorial
./re x200

(Details will depend on the particular test case.)

---

## 8. Choosing a MESA Version (Optional)

By default, the scripts start the latest supported MESA release in the Docker image. If you want a specific version (for reproducibility or to match a paper), many scripts support a -v option, for example:

./win_dockerMESA.sh -v r24.03.1
./mac_dockerMESA.sh -v 9793

In this course you can simply use the default version unless told otherwise.

---

## 9. Common Problems and Quick Fixes

### No graphical windows

- Ensure XQuartz/Xming/VcXsrv is running.
- Restart your X server and Docker Desktop if needed.
- Re-run the MESA-Docker script and then ./rn.

### docker_work not syncing with host

- Verify that MESA-Docker/docker_work exists on your host.
- If files appear only inside the container and not on the host, there may have been an issue mounting the volume the first time.
- In that case, you may need to:
  - Stop all containers,
  - Remove the MESA-Docker image,
  - Re-run the start script to recreate everything cleanly.

### Disk space issues

- Check Docker images:

  docker images

- Remove unused images with:

  docker rmi <image-id-or-name>

If you are on macOS and Docker seems to keep a very large disk image even after pruning, Docker Desktop has a “Reset to factory defaults” option in its settings. Use it with care: it deletes all local images and containers.

---

## 10. Next Steps

Once you can:

- Start the MESA container,
- Run the tutorial example (./mk, ./rn),
- Edit inlist_project and see changes in the plots and logs,

you are ready to move on to the MESA exercises for this course (HR diagrams, lifetime vs. mass, internal structure, etc.).
