# Robot programming: FANUC KAREL and RoboDK assignments

**Programiranje robota**

> Course exercises – Robot Programming (Programiranje robota), FSB, University of Zagreb, 2025/2026

![FANUC KAREL](https://img.shields.io/badge/language-FANUC%20KAREL-FFD700)
![Roboguide](https://img.shields.io/badge/sim-FANUC%20Roboguide-FFD700)
![RoboDK](https://img.shields.io/badge/sim-RoboDK-0a5c9e)
![Python](https://img.shields.io/badge/Python-3-3776AB?logo=python&logoColor=white)

Assignments for the Robot Programming course. Three tasks are written in FANUC KAREL and TP
and run in FANUC Roboguide: palletising, a data logger that streams robot state over Telnet, and
drilling at coordinates read from a file. The RoboDK part compares motion types through the
Python API and uses the Image Processing Editor add-in with fiducial markers and a simulated camera.

## What's inside

### FANUC Roboguide (KAREL and TP)

| Folder | What it does |
|---|---|
| `code/src/zadatak_1` | Palletising. A KAREL program generates a 4×4 grid of 16 pallet positions at 100 mm spacing into PR[20]–PR[35], asks for the place, user frame and speed on a user screen, and the TP program moves the robot there. |
| `code/src/zadatak_2` | Data logger. A main program runs the palletising program and a logger as parallel tasks, and the logger sends date and time, the active user frame, the TCP pose and the joint positions to the Telnet console. |
| `code/src/zadatak_3` | Drilling. A KAREL program reads `X Y speed [depth]` lines from `KOORDINATE.txt` (up to 200 points, default depth 25 mm) and calls a TP program that positions and drills at each point. |

### RoboDK

| Path | What it does |
|---|---|
| `robodk/src/zadatak_1.py` | Moves a robot between two targets with MoveL, MoveJ and MoveC, records the tool pose and joint values, and plots tool position, joint angles, velocities and accelerations. |
| `robodk/outputs/zadatak_01` | The four plots produced by the script. |
| `robodk/robodk_stations` | RoboDK stations for tasks 1 and 3, and a second set of the task 1 plots in `Robot_Analysis_Results`. |
| `robodk/zadatak_2` | RoboDK station for task 2. |
| `robodk/data/raw/video` | Two video recordings from the augmented reality task, one of them marked `-AR`. |
| `robodk/markers` | ArUco and ChArUco markers used in the image processing task. |

### Other folders

| Path | Contents |
|---|---|
| `report/` | The RoboDK report in Croatian ([PDF](report/Kresimir_Hartl_RoboDK.pdf)): motion comparison graphs, the Image Processing Editor add-in, fiducial markers, camera set-up and calibration, and the augmented reality pipeline. |

## How to run

The assignment texts, the course notes, the FANUC manuals and the example programs from the
course are not in the repository.

**KAREL tasks.** Build each `.kl` file in Roboguide's Karel Tool (the compiled `.pc` files are not
in the repository), download it to the controller, load the matching `.ls` TP program and start
the KAREL program from the SELECT menu. The step by step instructions for each task are in
[`code/src/README.md`](code/src/README.md) (Croatian).

**RoboDK motion comparison.** Open `robodk/robodk_stations/zadatak_1.rdk` in RoboDK. The station
needs the targets `Point_1`, `Point_2` and `Mid_Point` and the tool `Tool 1`. Then run:

```bash
cd robodk
python src/zadatak_1.py
```

The plots are written to `robodk/outputs/zadatak_01`.

## License

[Apache-2.0](LICENSE). It covers the author's own work in this repository. Third-party parts keep their own licences.
