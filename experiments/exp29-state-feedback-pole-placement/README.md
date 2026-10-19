# Final Experiment (Lab 29): State Feedback & Pole Placement

* **Original Report**: [`控制實習期末實驗報告.pdf`](./控制實習期末實驗報告.pdf)
* **Course**: Control Systems Laboratory — Final Project Report (Class 3A)
* **Author**: Yi-Wei Su (`0116C031`)

---

## 1. Objective

Design and verify a **Full-State Feedback Controller** using **Pole Placement (Pole Assignment)** in state space for a DC servo motor positioning system. Compare modern state-space pole assignment (`place` / `acker` in MATLAB and Simulink) against classical parameter tuning from Experiment 05 across five prescribed $(\omega_n, \zeta)$ dynamic specifications.

---

## 2. State-Space Mathematical Formulation

### 2.1 Open-Loop DC Servo Motor Model
Given the simplified DC servo motor transfer functions from armature voltage $V_a(s)$ to angular velocity $\omega(s)$ and angular position $\theta(s)$:

$$\frac{\omega(s)}{V_a(s)} = \frac{100}{s + 4}, \qquad \frac{\theta(s)}{V_a(s)} = \frac{100}{s(s + 4)}$$

Choosing the state vector $\mathbf{x}(t) = \begin{bmatrix} x_1(t) \\ x_2(t) \end{bmatrix} = \begin{bmatrix} \theta(t) \\ \omega(t) \end{bmatrix}$ and control input $u(t) = V_a(t)$, the open-loop state-space representation is:

$$\begin{bmatrix} \dot{x}_1(t) \\ \dot{x}_2(t) \end{bmatrix} = \underbrace{\begin{bmatrix} 0 & 1 \\ 0 & -4 \end{bmatrix}}_{A} \begin{bmatrix} x_1(t) \\ x_2(t) \end{bmatrix} + \underbrace{\begin{bmatrix} 0 \\ 100 \end{bmatrix}}_{B} u(t), \qquad y(t) = \underbrace{\begin{bmatrix} 1 & 0 \end{bmatrix}}_{C} \mathbf{x}(t)$$

---

### 2.2 Closed-Loop Full-State Feedback Design
Applying the linear state-feedback control law with reference input $r(t)$:

$$u(t) = -\mathbf{K}\mathbf{x}(t) + r(t) = -\begin{bmatrix} \delta_2 & \delta_1 \end{bmatrix} \begin{bmatrix} x_1(t) \\ x_2(t) \end{bmatrix} + r(t)$$

yields the closed-loop state equation:

$$\begin{bmatrix} \dot{x}_1(t) \\ \dot{x}_2(t) \end{bmatrix} = \underbrace{\begin{bmatrix} 0 & 1 \\ -100\delta_2 & -4 - 100\delta_1 \end{bmatrix}}_{A_{\text{cl}} = A - B\mathbf{K}} \begin{bmatrix} x_1(t) \\ x_2(t) \end{bmatrix} + \begin{bmatrix} 0 \\ 100 \end{bmatrix} r(t)$$

Equating the closed-loop characteristic polynomial $\det(sI - A_{\text{cl}})$ to the desired second-order characteristic equation $s^2 + 2\zeta\omega_n s + \omega_n^2 = 0$:

$$s^2 + (4 + 100\delta_1)s + 100\delta_2 = s^2 + 2\zeta\omega_n s + \omega_n^2 = 0$$

gives the exact analytical pole-placement gain formulas for the velocity feedback gain $\delta_1$ (`G1`) and position feedback gain $\delta_2$ (`G2`):

$$\delta_1 = \frac{2\zeta\omega_n - 4}{100}, \qquad \delta_2 = \frac{\omega_n^2}{100}$$

---

## 3. Five Pole-Placement Specifications & Simulation Results

| Spec | Target $\omega_n$ (rad/s) | Target $\zeta$ | Closed-Loop Poles $s_{1,2}$ | Velocity Gain $\delta_1$ (`G1`) | Position Gain $\delta_2$ (`G2`) | Transient Behavior & Engineering Notes |
| :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| **Spec 1** | $10$ | $0.200$ | $-2 \pm j9.798$ | $0$ | $1$ | **Baseline Unity Position Feedback**: High overshoot ($\approx 52.7\%$) and pronounced oscillation ($T_s \approx 2.0\text{ s}$). |
| **Spec 2** | $10$ | $0.400$ | $-4 \pm j9.165$ | $0.0400$ | $1$ | **Moderate Underdamping**: Adding velocity feedback $\delta_1 = 0.04$ cuts overshoot to $\approx 25.4\%$ and halves settling time ($T_s \approx 1.0\text{ s}$). |
| **Spec 3** | $50$ | $0.400$ | $-20 \pm j45.826$ | $0.3600$ | $25$ | **High-Bandwidth Scaling ($5\times\;\omega_n$)**: Same overshoot ($\%OS \approx 25.4\%$) as Spec 2, but $5\times$ faster rise and peak times. *(Note: Since $\delta_2 = 25$, the DC gain from $r(t)$ to $y(t)$ is $1/\delta_2 = 1/25$, so reference $r(t)$ is scaled by $\delta_2 = 25$ in Simulink to normalize final output to $1.0$.)* |
| **Spec 4** | $10$ | $0.707$ | $-7.07 \pm j7.07$ | $0.1014$ | $1$ | **Optimal Underdamped (Butterworth Configuration)**: Minimal overshoot ($\approx 4.3\%$) with rapid rise and settling ($T_s \approx 0.57\text{ s}$). *(Clarification: The bullet list on p. 6 of the Chinese report labels $\zeta = 0.707$ as "臨界阻尼" and $\zeta = 1.0$ as "過阻尼"; strictly speaking in control theory, $\zeta = 0.707 = 1/\sqrt{2}$ is **optimal underdamped**, $\zeta = 1.0$ is **critically damped**, and $\zeta > 1.0$ is **overdamped**.)* |
| **Spec 5** | $10$ | $1.000$ | $-10,\; -10$ | $0.1600$ | $1$ | **Critically Damped**: Repeated real poles at $s = -10$; fastest monotonic response with strictly zero overshoot ($0\%$). |

---

## 4. Comparison: Classical Parameter Tuning (Lab 05) vs. Modern State Feedback (Lab 29)

* **Design Philosophy**: In Experiment 05, the second-order response was observed passively by manually turning potentiometers (`a`, `b`, `T` on `CM08`) that altered the physical plant itself. In contrast, Experiment 29 keeps the physical DC servo motor plant fixed ($A, B$ constant) and **actively synthesizes a state-feedback gain vector $\mathbf{K} = [\delta_2,\; \delta_1]$** to relocate the closed-loop eigenvalues of $A - B\mathbf{K}$ to any desired location in the complex $s$-plane (provided $(A, B)$ is controllable).
* **Degrees of Freedom**: Measuring both states—angular position $x_1 = \theta(t)$ and angular velocity $x_2 = \omega(t)$—grants independent control over both coefficients of the second-order characteristic polynomial: $\delta_2$ sets the natural frequency $\omega_n$, while $\delta_1$ independently sets the damping ratio $\zeta$.
