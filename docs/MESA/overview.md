# MESA — Overview

**MESA (Modules for Experiments in Stellar Astrophysics)** is a modern, open-source 1D stellar evolution code. With MESA you can:

- Evolve stars from pre-main sequence to advanced stages.
- Change initial mass, metallicity, rotation, and mass loss to see how stellar structure responds.
- Model many different systems: low-mass stars, massive stars, white dwarfs, binaries, etc.

MESA is written mainly in Fortran and organized into self-contained *modules* (e.g. `star`, `binary`, `eos`, `kap`, `nuc`), which makes it flexible and extensible.

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

You *do not* need to modify the MESA source code itself for this course.

---

## Roadmap of This Section

- **Installation**  
  Step-by-step setup of Docker and MESA-Docker on Windows, macOS, and Linux.

- **Running MESA**  
  How to start the container, run your first model, and manage your working directory.

Once you complete those two pages, you’ll be ready to start the actual astrophysical exercises.
