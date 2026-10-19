# Experiment 12: PID Controller Tuning with Ziegler–Nichols Methods

* **Original Report**: [`控制實習報告12_PID控制器實驗.pdf`](./控制實習報告12_PID控制器實驗.pdf)
* **Course**: Control Systems Laboratory (Class 3A, Group 12)
* **Authors**: Hsing-Cheng Liu (`0116C001`), Yi-Wei Su (`0116C031`)

---

## 1. Objective

Implement and compare the two classic **Ziegler–Nichols (Z-N) heuristic PID tuning rules**—the **First Method (Open-Loop Process Reaction Curve)** and the **Second Method (Closed-Loop Ultimate Sensitivity)**—on third-order analog plants, followed by empirical hardware fine-tuning to achieve fast, low-overshoot step tracking.

---

## 2. Ziegler–Nichols Tuning Rules

For a parallel PID controller:

$$C(s) = K_P \left(1 + \frac{1}{T_I s} + T_D s\right) = K_P + \frac{K_I}{s} + K_D s, \qquad K_I = \frac{K_P}{T_I}, \qquad K_D = K_P T_D$$

Both Ziegler–Nichols rules target a **quarter-decay ratio** ($\text{decay ratio} \le 25\%$, corresponding to $\zeta \approx 0.22$, which typically produces an initial step overshoot between $10\%$ and $60\%$ prior to fine-tuning):

| Controller Type | Z-N 1st Method (Reaction Curve: $K, \tau, \tau_d$) | Z-N 2nd Method (Ultimate Gain & Period: $K_u, P_u$) |
| :--- | :--- | :--- |
| **P** | $K_P = \dfrac{\tau}{K \tau_d},\; T_I = \infty,\; T_D = 0$ | $K_P = 0.5 K_u,\; T_I = \infty,\; T_D = 0$ |
| **PI** | $K_P = 0.9\dfrac{\tau}{K \tau_d},\; T_I = \dfrac{\tau_d}{0.3},\; T_D = 0$ | $K_P = 0.45 K_u,\; T_I = \dfrac{P_u}{1.2},\; T_D = 0$ |
| **PID** | $K_P = 1.2\dfrac{\tau}{K \tau_d},\; T_I = 2\tau_d,\; T_D = 0.5\tau_d$ | $K_P = 0.6 K_u,\; T_I = 0.5 P_u,\; T_D = 0.125 P_u$ |

---

## 3. Experimental Implementation & Fine-Tuning

### 3.1 Part 1: Ziegler–Nichols First Method (Process Reaction Curve)
* **Third-Order Plant**:
  $$G_1(s) = \frac{1000}{(s + 10)(s^2 + 20s + 100)}$$
  Synthesized using `CM08` ($bT = 100, aT = 20$), `CM05` ($K = 1$), and `CM06` ($T = 10$ in `OP` mode).
* **Open-Loop S-Curve Measurement**:
  From the inflection-point tangent on the oscilloscope reaction curve (p. 7 of report):
  $$\tau_d \approx 0.08\text{ s}, \qquad \tau \approx 0.46\text{ s}, \qquad K = 1$$
* **Initial Z-N PID Gains vs. Hardware Fine-Tuned Gains**:
  * **Initial Z-N Calculation** *(as recorded in report Table on p. 7)*:
    $$K_P = 5.7, \quad T_I = 2\tau_d = 0.16\text{ s}, \quad T_D = 0.5\tau_d = 0.04\text{ s} \implies K_I = \frac{K_P}{T_I} = 35.625, \quad K_D = K_P T_D = 0.228$$
    *(Technical Note: Direct evaluation of $1.2 \times 0.46 / 0.08$ gives $K_P = 6.9$; the value $K_P = 5.7$ reflects the intermediate tangent reading $\tau \approx 0.38\text{ s}$ used during the lab session. With $K_I = 35.625$, the closed-loop response still exhibited substantial overshoot.)*
  * **Fine-Tuned Hardware Gains**: Reducing integral action and increasing derivative damping to **$K_P = 5,\; K_I = 1,\; K_D = 9$** eliminated the large transient overshoot on the physical oscilloscope, though Simulink comparison revealed some discrepancy attributable to analog module tolerances at high derivative gain.

---

### 3.2 Part 2: Ziegler–Nichols Second Method (Ultimate Sensitivity)
* **Plant**:
  $$G_2(s) = \frac{10(s + 100)}{(s + 10)^3} = \frac{100}{s^2 + 20s + 100} \times \frac{\frac{s}{100} + 1}{\frac{s}{10} + 1}$$
  Synthesized using `CM09` ($T_p = 10, T_z = 20$) and `CM08` ($aT = 20, bT = 100$).
* **Closed-Loop Critical Oscillation & Troubleshooting**:
  * Measured critical oscillation period $P_u \approx 0.8\text{ s}$ on the oscilloscope.
  * Initial Z-N PID calculation (p. 9): $K_P = 0.684,\; T_I = 0.145\text{ s},\; T_D = 0.03625\text{ s} \implies K_I = 1.71,\; K_D = 0.0684$.
  * **Hardware Troubleshooting Note**: During one intermediate test configuration ($K_P = 10, K_I = 0, K_D = 2.4$, Table 2 on p. 6), the oscilloscope trace dropped out completely due to a hardware fault in the analog PID suitcase module (`CM02`/`CM04`) and loop gain saturation. By adjusting the series gain on `CM05` and fine-tuning the PID parameters to **$K_P = 1.3,\; K_I = 1,\; K_D = 5$**, stable closed-loop tracking with minimal overshoot was successfully restored in both hardware and Simulink.

---

## 4. Comparison of Ziegler–Nichols 1st vs. 2nd Methods

| Criterion | First Method (Reaction Curve) | Second Method (Ultimate Sensitivity) |
| :--- | :--- | :--- |
| **Test Configuration** | Open-loop step test | Closed-loop proportional-only ($P$) test |
| **Measured Quantities** | Delay time $\tau_d$, time constant $\tau$, static gain $K$ | Ultimate gain $K_u$, ultimate oscillation period $P_u$ |
| **Operational Safety** | **High** — does not drive the system into sustained oscillation | **Moderate/Low** — requires bringing the plant to marginal stability |
| **Measurement Sensitivity** | Sensitive to visual tangent construction and noise on the S-curve | More repeatable frequency/gain reading once sustained oscillation is reached |
| **Applicability** | Strictly requires an S-shaped open-loop step response (inapplicable to integrating or oscillatory plants) | Applicable to a wider class of closed-loop systems, especially in simulation or non-hazardous testbeds |
