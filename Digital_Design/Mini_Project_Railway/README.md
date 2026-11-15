# Automatic Railway Level Crossing Controller

## Overview

The Automatic Railway Level Crossing Controller is an FSM-based digital control system designed to manage railway crossing operations. It monitors train approach and departure signals along with gate-position feedback to coordinate gate operation, warning indications, and road traffic signals.

The controller implements six operating states and incorporates a warning counter and fault-detection logic to handle selected invalid sensor combinations. The project covers Verilog RTL design, functional simulation, waveform analysis, code coverage, timing constraints, and logic synthesis.

## Objectives

- Design a finite-state-machine-based railway level crossing controller.
- Implement the controller using Verilog HDL.
- Coordinate railway gate operation, warning indications, and road traffic signals.
- Detect selected invalid gate-sensor combinations and handle fault conditions.
- Verify normal and fault operation using Synopsys VCS.
- Analyze simulation waveforms using GTKWave.
- Evaluate code coverage using line, toggle, condition, branch, and FSM metrics.
- Synthesize the RTL using Synopsys Design Compiler and analyze the generated reports.

## Input and Output Signals

| Signal | Direction | Function |
|---|---|---|
| `clk` | Input | Synchronizes FSM state transitions |
| `rst` | Input | Resets the controller |
| `train_approach` | Input | Indicates an approaching train |
| `train_departed` | Input | Indicates train departure |
| `gate_open` | Input | Gate-open position feedback |
| `gate_closed` | Input | Gate-closed position feedback |
| `gate_open_cmd` | Output | Requests gate opening |
| `gate_close_cmd` | Output | Requests gate closing |
| `warning` | Output | Activates the warning indication |
| `road_red` | Output | Stops road traffic |
| `road_green` | Output | Permits road traffic |

## FSM Design

The controller uses a six-state finite state machine.

| State | Encoding | Description |
|---|---|---|
| `IDLE` | `000` | Normal operating state with the gate open |
| `WARNING_ST` | `001` | Warning interval before gate closing |
| `CLOSING` | `010` | Gate closing sequence |
| `TRAIN_PASS` | `011` | Train passage through the crossing |
| `OPENING` | `100` | Gate opening sequence |
| `FAULT` | `101` | Fault state for selected invalid sensor conditions |

### State Operation

1. **IDLE:** After reset, the controller enters the normal state. The gate-open command is active and road traffic is permitted.
2. **WARNING_ST:** When a train approaches, the controller activates the warning indication and changes the road signal to red. A counter controls the warning interval.
3. **CLOSING:** After the warning interval, the controller requests gate closure and waits for gate-closed feedback.
4. **TRAIN_PASS:** Once the gate is confirmed closed, the controller remains in this state while the train passes.
5. **OPENING:** When train departure is detected, the controller requests gate opening.
6. **FAULT:** Selected invalid gate-sensor combinations cause the controller to enter the fault state. The fault state is retained until reset, with gate-close, warning, and road-red indications active.

After gate-open feedback is received during the opening sequence, the controller returns to `IDLE`.

## Verification

The design is verified using a Verilog testbench that generates a 10 ns clock and applies different operating scenarios.

The verification scenarios include:

1. Reset and initial signal conditions.
2. Train approach detection.
3. Warning interval operation.
4. Gate closing sequence.
5. Confirmation of gate-closed feedback.
6. Train passage and departure.
7. Gate opening and return to `IDLE`.
8. Invalid gate-sensor combinations and fault-state operation.

Synopsys VCS is used for RTL simulation, while GTKWave is used to inspect the generated waveforms.

### Running a Simulation

The following commands can be used from the project root directory with Icarus Verilog:

```bash
iverilog -o railway_controller_sim src/railway_controller.v tb/tb_railway.v
vvp railway_controller_sim
```

To inspect the waveform, ensure that the testbench contains the appropriate `$dumpfile` and `$dumpvars` statements. Open the generated VCD file using:

```bash
gtkwave railway_controller.vcd
```

### VCS-Based Simulation

```bash
vcs -o railway_controller_sim src/railway_controller.v tb/tb_railway.v
./railway_controller_sim
```

The waveform filename and location depend on the `$dumpfile` statement in the testbench.

## Coverage Analysis

Code coverage is collected using Synopsys VCS with line, toggle, branch, condition, and FSM coverage metrics.

| Coverage Metric | Coverage Achieved |
|---|---:|
| Line Coverage | 89.32% |
| Toggle Coverage | 80.56% |
| Condition Coverage | 63.64% |
| Branch Coverage | 75.86% |
| FSM Coverage | 42.86% |

The coverage analysis evaluates the execution of the controller's RTL statements, signal transitions, decision conditions, branches, and FSM states.

The verification scenarios exercise reset, train arrival, warning generation, gate closing, train passage, train departure, gate opening, and fault handling.

### VCS Coverage Commands

```bash
vcs -cm line+tgl+branch+cond+fsm \
    -o railway_controller_cov \
    src/railway_controller.v tb/tb_railway.v

./railway_controller_cov -cm line+tgl+branch+cond+fsm

urg -dir railway_controller_cov.vdb -report coverage_report
```

The coverage database and generated reports are produced by VCS and URG.

## Synthesis Constraints

The design uses Synopsys Design Compiler with the SAED32 standard-cell library for logic synthesis.

The timing constraints define:

- Clock period: 10 ns
- Input delay: 1.0 ns
- Output delay: 1.0 ns
- Reset: Excluded from normal timing analysis using a false-path constraint

The SDC constraints define the timing relationship between the controller and its external input and output signals.

## Design Compiler Synthesis

The synthesis flow uses Synopsys Design Compiler to read and elaborate the RTL, link the required technology libraries, apply timing constraints, check the design, and execute `compile_ultra`.

The synthesis analysis includes:

- Quality-of-results report
- Cell area report
- Timing report
- Power report
- Technology-mapped Verilog netlist
- Output SDC
- DDC design database

The synthesis flow uses library and script paths specific to the development environment. These paths must be updated when reproducing the synthesis flow on another system.

## Conclusion

The Automatic Railway Level Crossing Controller demonstrates the design and verification of an FSM-based digital control system using Verilog HDL.

The controller coordinates train detection, railway gate operation, warning indications, and road traffic signals while incorporating a warning counter and fault-detection logic.

Functional verification was performed using Synopsys VCS, and simulation waveforms were analyzed using GTKWave. Code coverage was evaluated using line, toggle, condition, branch, and FSM metrics. The RTL was subsequently synthesized using Synopsys Design Compiler with the SAED32 standard-cell library.

## Tools and Technologies

- Verilog HDL
- Synopsys VCS
- GTKWave
- Synopsys Design Compiler
- SAED32 Standard-Cell Library
- Icarus Verilog
