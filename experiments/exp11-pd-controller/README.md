# Experiment 11: Proportional-Derivative (PD) Controller Analysis

* **Original Report**: [`控制實習報告11_PD控制器.pdf`](./控制實習報告11_PD控制器.pdf)
* **Course**: Control Systems Laboratory (Class 3A, Group 12)
* **Authors**: Hsing-Cheng Liu (`0116C001`), Yi-Wei Su (`0116C031`)

---

## 1. Objective

Evaluate how combining **Proportional ($K_P$)** and **Derivative ($K_D$)** control actions shapes closed-loop transient and steady-state behavior in first-order (speed) and second-order (position) systems, demonstrating how anticipatory derivative feedback increases effective damping and suppresses overshoot without increasing the system order.

---

## 2. Hardware Configuration & Experimental Matrix

The PD controller $C(s) = K_P + K_D s$ was constructed in parallel using `CM02` ($K_P$) and `CM04` ($K_D s$) summed through `CM05` ($K = 1$), driving the `CM08` plant ($T = 10, a = 5, b = 5$) and the DC servo motor unit under a $0.1\text{ Hz}, 1\text{ V}$ square-wave step input (`CM10`).

### Part 1: Closed-Loop Speed Control (First-Order Plant)
* **Case 1(1) — Fixed $K_P = 1$**: Varied $K_D \in \{0,\; 0.2,\; 0.5\}$.
* **Case 1(2) — Fixed $K_D = 1$**: Varied $K_P \in \{0,\; 2,\; 10\}$.

### Part 2: Closed-Loop Position Control (Second-Order Plant)
* **Case 2(1) — Fixed $K_P = 1$**: Varied $K_D \in \{0.1,\; 0.2,\; 1.0\}$.
* **Case 2(2) — Fixed $K_D = 1$**: Varied $K_P \in \{2,\; 5,\; 10\}$.

*(Note: On page 6 of the original report, the section header for Part 2 reads "模擬 PI 控制器用於閉迴路直流司服馬達位置控制" due to a template typo, whereas all data rows $K_D = 0.1, 0.2, 1$ and analyses correspond to the PD controller.)*

---

## 3. Analytical & Experimental Findings

### 3.1 First-Order Plant with PD Control
For the first-order plant $G_p(s) = \frac{50}{s + 50}$, adding PD control yields the closed-loop transfer function:

$$M_1(s) = \frac{50 K_D s + 50 K_P}{(K_D + 1)s + (50 + 50 K_P)}$$

* **Steady-State Error Invariance to $K_D$**: Applying the Final Value Theorem ($s \to 0$), the derivative term $50 K_D s$ vanishes at steady state, leaving:
  $$e_{\text{step}}(\infty) = 1 - \frac{50 K_P}{50 + 50 K_P} = \frac{1}{1 + K_P}$$
  At fixed $K_P = 1$, the steady-state error remains $e_{ss} = 0.5$ regardless of $K_D \in \{0, 0.2, 0.5\}$. Reducing steady-state error in a Type 0 plant requires increasing $K_P$ (e.g., $e_{ss}$ drops from $1/3$ at $K_P=2$ to $1/11$ at $K_P=10$).
* **Effective Time Constant**: Because the denominator coefficient of $s$ becomes $(K_D + 1)$ in this first-order model, increasing $K_D$ at fixed $K_P = 1$ slightly lengthens the effective time constant ($T_r$ increases from $0.022\text{ s}$ at $K_D=0$ to $0.033\text{ s}$ at $K_D=0.5$).

---

### 3.2 Second-Order Plant with PD Control
For a second-order plant, adding derivative action introduces an open-loop zero and increases the $s^1$ damping coefficient in the closed-loop characteristic polynomial without raising the system order:

* **Comparison of P-Only vs. PD Control ($K_D = 1$)**:
  * **P-Only**: $M(s) = \frac{s^2 + 50s}{s^2 + 50s + 50 K_P} \implies 2\zeta\omega_n = 50$
  * **PD ($K_D = 1$)**: $M(s) = \frac{50s + 50 K_P}{s^2 + 100s + 50 K_P} \implies 2\zeta\omega_n = 100$

| $K_P$ | Natural Frequency $\omega_n$ (rad/s) | Damping Ratio $\zeta$ (P-Only) | Damping Ratio $\zeta$ (PD, $K_D = 1$) | Steady-State Step Error $e_{ss}$ |
| :---: | :---: | :---: | :---: | :---: |
| **$2$** | $10$ | $2.50$ | $5.00$ | $0$ |
| **$5$** | $5\sqrt{10} \approx 15.81$ | $1.58$ | $\sqrt{10} \approx 3.16$ | $0$ |
| **$10$** | $10\sqrt{5} \approx 22.36$ | $1.118$ | $\sqrt{5} \approx 2.236$ | $0$ |

* **Key Engineering Takeaway**: In second-order position control (such as robotic arm positioning, UAV attitude stabilization, and high-speed servo drives), increasing $K_P$ speeds up response ($\omega_n \propto \sqrt{K_P}$) but erodes the damping ratio ($\zeta \propto K_P^{-1/2}$). Adding derivative action ($K_D$) directly boosts the damping ratio $\zeta$, allowing higher $K_P$ gains and faster rise times while suppressing overshoot and mechanical vibration.
