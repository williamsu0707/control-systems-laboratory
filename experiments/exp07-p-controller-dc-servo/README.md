# Experiment 07: Proportional (P) Controller — DC Servo Motor Speed & Position Control

* **Original Report**: [`控制實習報告7.pdf`](./控制實習報告7.pdf)
* **Course**: Control Systems Laboratory (Class 3A, Group 12)
* **Authors**: Hsing-Cheng Liu (`0116C001`), Yi-Wei Su (`0116C031`)

---

## 1. Objective

Investigate the closed-loop transient and steady-state behavior of a **Proportional (P) Controller** ($K_P$) applied to both:
1. **DC Servo Motor Speed Control** (modeled as a 1st-order Type 0 system via tachometer feedback $V_\omega$), and
2. **DC Servo Motor Angular Position Control** (modeled as a 2nd-order Type 1 system via potentiometer feedback $V_\theta$).

---

## 2. Hardware Calibration & System Models

Before closed-loop testing, the DC servo motor was zero-calibrated by switching the servo amplifier toggle to internal control, adjusting the zero-offset potentiometer until the shaft remained stationary, and returning the switch to external control with the cooling fan active.

### 2.1 Part 1: First-Order Speed Control
* **Open-Loop Plant**: $G_1(s) = \frac{50}{s + 50}$ with proportional gain $K_P \in \{1, 2, 5, 10\}$ (`CM02`).
* **Closed-Loop Transfer Function**:
  $$M_1(s) = \frac{K_P G_1(s)}{1 + K_P G_1(s)} = \frac{50 K_P}{s + 50(1 + K_P)}$$
* **Closed-Loop Pole**: $p = -50(1 + K_P) = -a$.
* **Performance Metrics**:
  $$T_r = \frac{2.2}{a}, \qquad T_s = \frac{4}{a}, \qquad e_{\text{step}}(\infty) = \frac{1}{1 + K_P}$$

| $K_P$ | Closed-Loop Pole $p = -a$ | Rise Time $T_r$ (s) | Settling Time $T_s$ (s) | Steady-State Error $e_{ss}$ |
| :---: | :---: | :---: | :---: | :---: |
| **$1$** | $-100$ | $0.0220$ | $0.0400$ | $1/2\; (50.0\%)$ |
| **$2$** | $-150$ | $0.0147^*$ | $0.0266$ | $1/3\; (33.3\%)$ |
| **$5$** | $-300$ | $0.0073$ | $0.0133$ | $1/6\; (16.7\%)$ |
| **$10$** | $-550$ | $0.0040$ | $0.00727$ | $1/11\; (9.1\%)$ |

*(Note: In Table 5 of the original report, $T_r$ for $K_P = 2$ was typographical printed as `0.1467` instead of `0.01467` s.)*

---

### 2.2 Part 2: Second-Order Position Control
* **Open-Loop Plant**: $G_2(s) = \frac{5}{s(s + 5)} = \frac{5}{s^2 + 5s}$ (Type 1 plant due to mechanical integration from angular velocity $\omega$ to angular position $\theta$).
* **Closed-Loop Transfer Function**:
  $$M_2(s) = \frac{5 K_P}{s^2 + 5s + 5 K_P} \equiv \frac{\omega_n^2}{s^2 + 2\zeta\omega_n s + \omega_n^2}$$
* **Characteristic Parameters**:
  $$\omega_n = \sqrt{5 K_P}, \qquad \zeta = \frac{5}{2\sqrt{5 K_P}}, \qquad e_{\text{step}}(\infty) = \frac{1}{1 + \lim_{s\to 0} K_P G_2(s)} = 0$$

| $K_P$ | Closed-Loop $M_2(s)$ | Natural Frequency $\omega_n$ (rad/s) | Damping Ratio $\zeta$ | Rise Time $T_r$ (s) | Settling Time $T_s$ (s) | Maximum Overshoot $\%OS$ | Steady-State Error $e_{ss}$ |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **$1$** | $\frac{5}{s^2 + 5s + 5}$ | $\sqrt{5} \approx 2.236$ | $1.118$ | $2.1$ | $3.4$ | $0\%$ *(Overdamped)* | $0$ |
| **$2$** | $\frac{10}{s^2 + 5s + 10}$ | $\sqrt{10} \approx 3.162$ | $0.790$ | $1.1$ | $1.6$ | $1.7457\%$ | $0$ |
| **$5$** | $\frac{25}{s^2 + 5s + 25}$ | $5.000$ | $0.500$ | $0.3276$ | $1.6$ | $16.30\%$ | $0$ |
| **$10$** | $\frac{50}{s^2 + 5s + 50}$ | $\sqrt{50} \approx 7.071$ | $0.354$ | $0.1867$ | $1.6$ | $30.45\%$ | $0$ |

---

## 3. Root Locus & Control Engineering Insights

* **First-Order Speed Loop**: Increasing $K_P$ pushes the single real closed-loop pole monotonically leftward along the negative real axis ($p = -(1+K_P)p_1$). Consequently, higher $K_P$ simultaneously improves rise time, settling time, and steady-state accuracy without ever inducing oscillation, limited in practice only by actuator saturation and sensor noise.
* **Second-Order Position Loop**: Because the plant is already Type 1, steady-state step error is identically zero for any $K_P > 0$. However, increasing $K_P$ moves the complex conjugate roots vertically away from the real axis along the vertical line $\sigma = -2.5$ (since $\zeta\omega_n = 2.5$ is constant). Thus, once $K_P > 1.25$ ($\zeta < 1$), higher $K_P$ speeds up rise time ($T_r$ drops from $2.1\text{ s}$ to $0.1867\text{ s}$) while keeping $T_s \approx \frac{4}{\zeta\omega_n} = 1.6\text{ s}$ constant and substantially increasing overshoot (up to $30.45\%$ at $K_P = 10$).
