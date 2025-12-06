# MESA — Overview

**MESA (Modules for Experiments in Stellar Astrophysics)** is a modern, open-source 1D stellar evolution code. With MESA you can:

- Evolve stars from pre-main sequence to advanced stages.
- Change initial mass, metallicity, rotation, and mass loss to see how stellar structure responds.
- Model many different systems: low-mass stars, massive stars, white dwarfs, binaries, etc.

MESA is written mainly in Fortran and organized into self-contained *modules* (e.g. `star`, `binary`, `eos`, `kap`, `nuc`), which makes it flexible and extensible.

---

## Why We Use Docker in This Course

Installing MESA “natively” (directly on your computer) can be difficult, especially on **Windows**:

- It needs a specific Fortran compiler,
- A compatible set of libraries,
- Correct environment variables and paths.

To keep the focus on **astrophysics** instead of **system administration**, we will use **MESA-Docker**:

> **MESA-Docker** = a ready-made Linux environment that already contains a full installation of MESA.

You will:

- Install Docker (once),
- Download the MESA-Docker repository (once),
- Start a pre-configured MESA container whenever you want to work.

If your computer can run Docker, you can run MESA, regardless of whether your host OS is **Windows**, **macOS**, or **Linux**.

---

## Mental Model: What Is Docker Doing?

You can imagine the setup like this:

Your computer (Windows / macOS / Linux)
    └── Docker Desktop / Docker Engine
         └── MESA-Docker container (Linux + preinstalled MESA)
              └── You run ./mk, ./rn, etc. inside this container

Inside the container you have:

- A full Linux system,
- A MESA installation referenced by the environment variable `MESA_DIR`,
- A working directory called `docker_work` that is **shared** with your real machine.

---

## MESA in This Course

In this course you will mainly:

1. **Run example problems** shipped with MESA:
   - For example, a 1 M⊙ star evolving off the main sequence.

2. **Modify inlists** (MESA input files):
   - Change initial mass,
   - Adjust metallicity,
   - Tweak mixing and mass-loss parameters,
   - Change when the model stops.

3. **Inspect MESA outputs**:
   - Text logs in `LOGS/history.data` and `LOGS/profile*.data`,
   - Plots from `pgstar` (HR diagrams, profiles, Kippenhahn diagrams, etc.).

4. **(Optionally) Build a small project**, such as:
   - Comparing evolution at different metallicities,
   - Studying the structure of massive stars at core collapse,
   - Following the growth of a white dwarf toward thermonuclear ignition.

You *do not* need to modify the MESA source code itself for this course, though advanced students are welcome to explore.

---

## What You Will Actually Use

Inside the container:

- `MESA_DIR`  
  A system-wide environment variable pointing to the main MESA directory (where the code and test suites live).

- `~/docker_work`  
  Your **personal working area**, which is synchronized to a folder called `docker_work` on your host machine.

Typical workflow:

1. Change to your shared working directory:

   cd ~/docker_work

2. Copy an example project:

   cp -r $MESA_DIR/star/work tutorial
   cd tutorial

3. Compile and run:

   ./mk   # build
   ./rn   # run

4. Edit `inlist_project` and rerun to explore parameter changes.

---

## Roadmap of This Section

- **Installation**  
  Step-by-step setup of Docker and MESA-Docker on Windows, macOS, and Linux.

- **Running MESA**  
  How to start the container, run your first model, and manage your working directory.

Once you complete those two pages, you’ll be ready to start the actual astrophysical exercises.
