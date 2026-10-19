# Experiment 05: Transient Response Specifications

* **Original Report**: [`控制實習報告5.pdf`](./控制實習報告5.pdf)
* **Course**: Control Systems Laboratory (Class 3A, Group 12)
* **Authors**: Hsing-Cheng Liu (`0116C001`), Yi-Wei Su (`0116C031`)

---

## 1. Objective

Establish standard time-domain performance specifications—**Delay Time ($T_d$)**, **Rise Time ($T_r$)**, **Peak Time ($T_p$)**, **Maximum Overshoot ($M_p$)**, and **Settling Time ($T_s$)**—as quantitative benchmarks for evaluating second-order control system quality, and verify how these metrics scale with $\zeta$ and $\omega_n$ in hardware and Simulink.

---

## 2. Transient Specification Definitions

For a step response $c(t)$ with steady-state value $c(\infty)$:
* **Delay Time ($T_d$)**: Time required for the response to reach $50\%$ of its final value the first time:
  $$T_d \approx \frac{1 + 0.7\zeta}{\omega_n}$$
* **Rise Time ($T_r$)**: Time required for the response to rise from $10\%$ to $90\%$ (or $0\%$ to $100\%$ for underdamped systems) of its final value.
* **Peak Time ($T_p$)**: Time required to reach the first (maximum) overshoot peak:
  $$T_p = \frac{\pi}{\omega_n\sqrt{1 - \zeta^2}}$$
* **Maximum Percent Overshoot ($M_p$)**:
  $$M_p = e^{-\zeta\pi / \sqrt{1 - \zeta^2}} \times 100\%$$
* **Settling Time ($T_s$, $2\%$ criterion)**:
  $$T_s \approx \frac{4}{\zeta\omega_n}$$

---

## 3. Experimental Matrix & Calculated Specifications

Using the `CM08` Second-Order Plant module with unity feedback ($G(s) = \frac{bT}{s^2 + aTs + bT}$) driven by a $0.1\text{ Hz}, 1\text{ V}$ square-wave step source (`CM10`):

| Case | $\omega_n$ (rad/s) | $\zeta$ | `CM08` $(T, b, a)$ | Delay Time $T_d$ (s) | Rise Time $T_r$ (s) | Settling Time $T_s$ (s) | Damping Regime |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| **1** | $10$ | $2.0$ | $(10,\; 10,\; 4.0)$ | — | — | $0.2^*$ | Overdamped |
| **2** | $10$ | $0.5$ | $(10,\; 10,\; 1.0)$ | $0.241$ | $0.241$ | $0.8$ | Underdamped |
| **3** | $10$ | $0.2$ | $(10,\; 10,\; 0.4)$ | $0.181$ | $0.181$ | $2.0$ | Underdamped |
| **4** | $5$ | $0.2$ | $(10,\; 2.5,\; 0.2)$ | $0.361$ | $0.361$ | $4.0$ | Underdamped |
| **5** | $2$ | $0.2$ | $(1,\; 4.0,\; 0.8)$ | $0.902$ | $0.902$ | $10.0$ | Underdamped |

*(Note: In Case 1, $4 / (\zeta\omega_n) = 0.2\text{ s}$ is tabulated from the standard formula $\frac{4}{\zeta\omega_n}$, though for an overdamped system with $\zeta = 2$, the slow dominant pole at $s = -\omega_n(\zeta - \sqrt{\zeta^2-1}) \approx -2.68$ governs actual physical settling.)*

---

## 4. Key Experimental Findings

* **Inverse Scaling with $\omega_n$**: Comparing Cases 3, 4, and 5 at fixed $\zeta = 0.2$, reducing $\omega_n$ from $10 \to 5 \to 2\text{ rad/s}$ scales $T_d$, $T_r$, and $T_s$ in exact inverse proportion ($1\times : 2\times : 5\times$), matching the oscilloscope time-base measurements.
* **Speed vs. Damping Trade-off**: At fixed $\omega_n = 10\text{ rad/s}$, lowering $\zeta$ from $0.5$ to $0.2$ reduces initial delay/rise time ($0.241\text{ s} \to 0.181\text{ s}$) at the cost of a $2.5\times$ longer settling time ($0.8\text{ s} \to 2.0\text{ s}$) due to lightly damped ringing.
