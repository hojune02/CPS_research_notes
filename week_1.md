# Week 1 (Mar 6–12) — Control Theory & CPS Architecture

## PID controller

PID stands for proportional, integral, and derivative. A controller's job is to reach the desired value. As shown in the formula given below, a system wants to reach `r(t)` by calculating the error (`e(t)`) between its current reading (`y(t)`) and `r(t)`.

```text
e(t) = r(t) - y(t)
```

The actuator activation to achieve this is defined as follows:

```text
u(t) = Kp x e(t) + Ki x accumulated e(t) + Kd x derivative of e(t) over time
```

Having just Kp results in steady-state error. The integral term eliminates it by accumulating the small residual error and causing more control action. The derivative term prevents overshoot when the system is approaching the target too rapidly.

Security implications is that attacks against CPS tampers with `y(t)`, resulting in fallacious system response to reach `r(t)`.

## State-space representation

Transfer functions does not reveal the internal system dynamics. State-space exposes them, giving clues for true state from observed state. Consider an environment where the true state vector is represented by `[room_temp, wall_temp]` and we can only observe `[room_temp]`.

```text
x'(t) = Ax(t) + Bu(t)
y(t) = Cx(t) + Du(t)
```

where `x(t)` is state vector, `u(t)` is input vector, `y(t)` is output vector. `A` represents system matrix which shows internal dynamics and coupling of components of `x(t)`. `B` shows how actuators affect the true state. `C` represents the visible component of `x(t)` to an observer. `D` represents feedthrough: how much the input vector changes the output `y` instantaneously.

Security implications is that CPS attacks tampers with the observed state, corrupting the operation of the state estimator.

## Lyapunov stability

The system is stable if an energy-like function that describes it always decreases as the system evolves.

## The Purdue Model

Level 0 indicates the physical process: how sensors and actuators function. 

Level 1 indicates PLCs and RTUs, which read Level 0 sensors and command Level 0 actuators. 

Level 2 indicates supervisory control, such as SCADA and DCS; they communicate with Level 1 using protocols such as Modbus, DNP3, etc.

Level 3 contains manufacting operations such as MES, historians, and batch management. The Industrial DMZ is in between Level 3 and 4, a firewall between OT and IT. IDMZ is being removed slowly due to integration with cloud connectivity and IIoT.

Level 4 contains enterprise IT such as corporate email network, ERP, and file servers. 

## PLC Architecture

A PLC is a computer that runs deterministic control logic in real time. It reads inputs, execute programs, write outputs, and does housekeeping (communication, diagnostics). This execution cycle continues to take place.

## Ladder Diagram

```text
     SENSOR_A    SENSOR_B       MOTOR
─────┤  ├─────────┤  ├──────────( )─────
     [NO]         [NO]

IF SENSOR_A AND SENSOR_B THEN energize MOTOR
```
Normally Open contact means that the current passes through when the variable is true.

## Structured Text

This is a high-level, human-readable form of representing the control logic. LLM-assisted vulnerability testing targets ST since it is structured enough for parsing.

## Function Block Diagram

Boxes represent functions such as PID block, sensor, timers, etc. 

## SCADA Fundamentals

Level 2 software that controls and monitors field devices. 

RTUs sit in remote field locations, collecting sensor data and reporting them back to the SCADA master. It mainly uses DNP3 for power/water utilities. 

PLCs sit in local environment, running the full control logic. It communicates using Modbus, Profibus, EtherNet/IP, IEC 61850 and so on. 

HMIs represent the screen that human operators look at. Operators issue commands to PLCs/RTUs. It uses OPC-UA or direct protocol drivers when pulling data from Level 1.

The SCADA master polls field devices for gathering sensor data nad issuing commands. Field devices respond to the master's request, except for unsolicited responses (alarms) and exception reporting -> These are DNP3 features.

Modbus is a dead-simple register read/write with no security. It has no authentication in base spec, and very simple to implement. DNP3 is used for RTUs which require long-distance capabilities; event data is timestamped with quality flags.