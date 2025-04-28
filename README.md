
#TTK4235--Elevator-Controll-System
** NTNU TTK4235 Elevator Control System – Real-time software for controlling an elevator using C**
## Project Overview

This repository hosts the fully implemented four-floor elevator control system in C. The system communicates with the SimElevatorServer simulator (or Arduino hardware) to manage motor movement, floor indicators, button lamps, door timing, and emergency stop/obstruction handling. The design follows a pragmatic V‑model with UML documentation.

Refer to `TTK4235_Heisprosjekt.pdf` for in-depth requirements, design diagrams, implementation details, and reflections.

## Repository Structure

```plain
Heisprosjekt/                     # Root folder for the elevator project
└── elevator_project/            # Main project directory
    ├── Makefile                 # Build: all, clean
    ├── elevio.con               # Elevator I/O config
    ├── simulator.con            # Simulator config
    ├── SimElevatorServer        # Simulator binary (Linux/macOS)
    ├── SimElevatorServer.exe    # Simulator binary (Windows)
    ├── heisrapport-gruppe78.pdf # Project report (requirements, design, reflection)
    ├── build/                   # Compiled executables and object files
    └── source/                  # Source code modules
        ├── elevator/            # I/O API: elevio.h, elevio.c
        ├── driver/              # Core logic: elevator.h, elevator.c
        ├── timer/               # Timing utilities: timer.h, timer.c
        └── main.c               # Main control loop
```

## Prerequisites

- C compiler (e.g., GCC, Clang)
- Make utility
- Linux/macOS or Windows environment
- Arduino hardware (only if testing on real device instead of simulator)

## Building

From the project root:

```bash
cd Heisprosjekt/elevator_project
make
```

- Compiles all modules: `source/elevator`, `source/driver`, `source/timer`, `main.c`.
- Outputs binaries into `build/`.

## Running

### Using Simulator

1. **Start the simulator**:
   ```bash
   ./SimElevatorServer

### On Real Hardware

- Update `elevio.con` with Arduino port settings.
- Connect Arduino and start `./main` from `build/`.

## Architecture & Design

- **V‑Model Process**: Ensures traceability from requirements (FAT test in report) through design, implementation, and verification.
- **State Machine**: Deterministic states: `INIT`, `IDLE`, `MOVING_UP`, `MOVING_DOWN`, `DOOR_OPEN`, `STOPPED_BETWEEN`.
- **UML Diagrams**:
  - **State Diagram** (see report §3.2) maps events to transitions.
  - **Class Diagram** (report §3.3) shows modules: `Elevator`, `Timer`, `ElevIO` and their relations.

## Module Summary

### `driver/elevator.h` & `elevator.c`

- **`Elevator` struct**: Tracks current/last floor, direction, door timer, order matrix, and state.
- **Key functions**:
  - `elevator_init()`: Calibrates elevator to a known floor.
  - `elevator_update_orders()`: Reads hall/cabin buttons and updates orders.
  - `elevator_should_stop_up/down()`: Evaluates if elevator must stop mid‑travel.
  - `elevator_clear_orders_at_floor()`: Clears completed orders and lamps.

### `timer/timer.h` & `timer.c`

- Provides real‑time support:
  - `timer_get_current_time()`: Fetches current timestamp.
  - `timer_expired(deadline)`: Checks door‑open deadlines.

### `elevator/elevio.h` & `elevio.c`

- Abstracts hardware/simulator I/O:
  - Button reads (`elevio_callButton`), floor sensor (`elevio_floorSensor`).
  - Motor control (`elevio_motorDirection`).
  - Indicator lamps and display (`elevio_doorOpenLamp`, `elevio_floorIndicator`, `elevio_stopLamp`).

### `main.c`

- Implements continuous control loop:
  1. Read sensors (stop, obstruction, floor).
  2. Update orders (unless emergency stop).
  3. Execute state machine (`switch` on `elev.state`).
  4. Update outputs (motor, lamps, display).
  5. Brief sleep (`usleep(1000)`).

## Cleaning Up

```bash
make clean
```
Removes all build artifacts in `build/`.

---
_For further details, consult `heisrapport-gruppe78.pdf` or contact the author._


