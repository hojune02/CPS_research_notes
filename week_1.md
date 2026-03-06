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
