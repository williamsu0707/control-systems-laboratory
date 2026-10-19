# Experiment 02: System Modeling & Damping Analysis

* **Original Report**: [`控制實習報告2.pdf`](./控制實習報告2.pdf)
* **Course**: Control Systems Laboratory (Class 3A, Group 12)
* **Authors**: Hsing-Cheng Liu (`0116C001`), Yi-Wei Su (`0116C031`)

---

## 1. Objective

Translate a physical second-order mechanical system (mass–spring–damper) into a linear ordinary differential equation (ODE), derive its analytical homogeneous and particular solutions across different damping regimes, and validate the theoretical trajectories against both **analog computer simulation** (using operational-amplifier integrator and gain modules) and **MATLAB/Simulink** models.

---

## 2. Mathematical Model

A translational mass–spring–damper system subjected to an external force $F(t)$ obeys Newton's second law:

$$m \ddot{x}(t) + c \dot{x}(t) + k x(t) = F(t)$$

Rewriting in standard second-order canonical form by dividing through by $m$:

$$\ddot{x}(t) + \frac{c}{m} \dot{x}(t) + \frac{k}{m} x(t) = \frac{1}{m} F(t) \quad \Longleftrightarrow \quad \ddot{x}(t) + 2\zeta\omega_n \dot{x}(t) + \omega_n^2 x(t) = \frac{1}{m} F(t)$$

where the natural frequency $\omega_n$ and damping ratio $\zeta$ are determined by the physical parameters $(m, c, k)$:

$$\omega_n = \sqrt{\frac{k}{m}}, \qquad \zeta = \frac{c}{2\sqrt{mk}}$$

Depending on the discriminant $c^2 - 4mk$ (or equivalently $\zeta$), the homogeneous solution $x_h(t)$ falls into one of three regimes:

1. **Overdamped ($c^2 > 4mk,\; \zeta > 1$)**: Two distinct real roots $\lambda_{1,2} < 0$, yielding non-oscillatory exponential decay:
   $$x_h(t) = a_1 e^{\lambda_1 t} + a_2 e^{\lambda_2 t} + k_1$$
2. **Critically Damped ($c^2 = 4mk,\; \zeta = 1$)**: Repeated negative real roots $\lambda_1 = \lambda_2 = \lambda$, providing the fastest non-oscillatory convergence:
   $$x_h(t) = (a_1 + a_2 t) e^{\lambda t} + k_1$$
3. **Underdamped ($c^2 < 4mk,\; 0 < \zeta < 1$)**: Complex conjugate roots $p \pm j q$ ($p = -\zeta\omega_n < 0,\; q = \omega_n\sqrt{1-\zeta^2}$), producing damped sinusoidal oscillation:
   $$x_h(t) = e^{pt}\bigl(a_1 \cos(qt) + a_2 \sin(qt)\bigr) + k_1$$

---

## 3. Experimental Setup & Parameter Configurations

The differential equation $\ddot{x}(t) = \frac{1}{m}F(t) - \frac{c}{m}\dot{x}(t) - \frac{k}{m}x(t)$ was synthesized in hardware using two cascaded analog integrators (`CM06-1` for $\dot{x}(t)$ and `CM06-2` for $x(t)$), a summing amplifier (`CM05` with gain $K = 1/m = 1$), and a dual feedback gain block (`CM07` providing $-K_1 = -c$ and $-K_2 = -k$).

| Experiment Case | Input $F(t)$ | Mass $m$ | Velocity IC $\dot{x}(0)$ (`CM06-1`) | Position IC $x(0)$ (`CM06-2`) | Damping $K_1 (c)$ (`CM07`) | Stiffness $K_2 (k)$ (`CM07`) | Damping Ratio $\zeta = c / 2$ |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Exp 1 (Homogeneous)** | $0\text{ V}$ | $1$ | $0$ | $5\text{ V}$ *(Simulink: $0, 5, -5$)* | $3,\; 2,\; 1$ | $1$ | $1.5,\; 1.0,\; 0.5$ |
| **Exp 2 (Forced Step)** | $1\text{ V}$ ($1\text{ Hz}$ square wave) | $1$ | $0$ | $0\text{ V}$ *(Simulink: $0, 5, -5$)* | $3,\; 2,\; 1$ | $1$ | $1.5,\; 1.0,\; 0.5$ |

---

## 4. Results & Parametric Sensitivity Analysis

### 4.1 Damping Coefficient $c$ ($m = 1, k = 1 \implies \zeta = c/2$)
* **$c = 3$ ($\zeta = 1.5$, Overdamped)**: Both oscilloscope traces and Simulink simulations exhibit sluggish, monotonic decay (Exp 1) or rise (Exp 2) without overshoot.
* **$c = 2$ ($\zeta = 1.0$, Critically Damped)**: Fastest convergence to equilibrium ($0\text{ V}$ in Exp 1, $1\text{ V}$ in Exp 2) with zero overshoot.
* **$c = 1$ ($\zeta = 0.5$, Underdamped)**: Clear overshoot and damped oscillation before settling. In Experiment 1 ($c=1$), the hardware oscilloscope trace exhibited slightly smaller oscillation amplitude than the ideal Simulink waveform due to non-ideal op-amp losses and component tolerances in the `CM06`/`CM07` analog blocks.

### 4.2 Spring Constant $k$ ($m = 1, c = 1 \implies \zeta = \frac{1}{2\sqrt{k}}$)
* Tested in Simulink across $k = 0.125$ ($\zeta = \sqrt{2} > 1$, Overdamped), $k = 0.25$ ($\zeta = 1$, Critically Damped), and $k = 4$ ($\zeta = 0.25 < 1$, Underdamped).
* Increasing $k$ not only shifts the system toward underdamped oscillation (since $\zeta \propto k^{-1/2}$) but also **reduces the steady-state displacement** inversely ($x_{ss} = F/k = 1/k$, yielding $8,\; 4,$ and $0.25$ respectively for a unit step input).

### 4.3 Mass $m$ ($c = 1, k = 1 \implies \zeta = \frac{1}{2\sqrt{m}}$)
* Tested in Simulink across $m = 0.125$ (Overdamped), $m = 0.25$ (Critically Damped), and $m = 4$ (Underdamped), noting that the Simulink gain block represents $1/m$.
* While increasing $m$ decreases $\zeta$ and slows the natural frequency $\omega_n = 1/\sqrt{m}$ (lengthening the oscillation period and settling time), the **steady-state value $x_{ss} = F/k$ remains completely invariant to $m$**.
