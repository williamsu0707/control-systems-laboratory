# Experiment 04: Second-Order System Time Response

* **Original Report**: [`控制實習報告4.1.pdf`](./控制實習報告4.1.pdf)
* **Course**: Control Systems Laboratory (Class 3A, Group 12)
* **Authors**: Hsing-Cheng Liu (`0116C001`), Yi-Wei Su (`0116C031`)

---

## 1. Objective

Examine how the two fundamental parameters of a canonical second-order system—**damping ratio ($\zeta$)** and **undamped natural frequency ($\omega_n$)**—independently shape the step response waveform, specifically the percent maximum overshoot ($\%OS$), peak time ($T_p$), and settling time ($T_s$).

---

## 2. Mathematical Model & Hardware Mapping

Using the `CM08` Second-Order Plant module inside a unity-feedback loop (`CM01`), the closed-loop transfer function is:

$$G(s) = \frac{C(s)}{R(s)} = \frac{B}{s^2 + As + B} = \frac{bT}{s^2 + aTs + bT} \equiv \frac{\omega_n^2}{s^2 + 2\zeta\omega_n s + \omega_n^2}$$

Matching coefficients links the hardware potentiometer settings $(a, b, T)$ directly to $(\omega_n, \zeta)$:

$$B = bT = \omega_n^2, \qquad A = aT = 2\zeta\omega_n$$

For an underdamped system ($0 < \zeta < 1$), the analytical percent maximum overshoot and peak time are:

$$\%OS = \frac{c_{\max} - c_{\text{final}}}{c_{\text{final}}} \times 100\% = e^{-\zeta\pi / \sqrt{1 - \zeta^2}} \times 100\%, \qquad T_p = \frac{\pi}{\omega_n \sqrt{1 - \zeta^2}}, \qquad T_s \approx \frac{4}{\zeta\omega_n}$$

---

## 3. Experimental Conditions & Quantitative Comparison

### Part 1: Varying $\zeta$ at Fixed $\omega_n = 10\text{ rad/s}$ ($T = 10, b = 10 \implies B = 100,\; a = 2\zeta$)
Tested $\zeta \in \{2.0,\; 1.0,\; 0.7,\; 0.2,\; 0\}$ by setting `CM08` parameter $a \in \{4,\; 2,\; 1.4,\; 0.4,\; 0\}$.

### Part 2: Varying $\omega_n$ at Fixed $\zeta = 0.2$
Tested $\omega_n \in \{10,\; 5,\; 2\}\text{ rad/s}$ while keeping $\zeta = 0.2$ constant by simultaneously scaling $a, b,$ and $T$:

| Test Case | $\omega_n$ (rad/s) | $\zeta$ | Hardware Settings $(T, b, a)$ | Closed-Loop $G(s)$ | Theoretical $\%OS$ | Theoretical Peak Time $T_p$ (s) | Approx. Settling Time $T_s \approx \frac{4}{\zeta\omega_n}$ (s) |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **Part 1-1** | $10$ | $2.0$ | $(10,\; 10,\; 4.0)$ | $\frac{100}{s^2 + 40s + 100}$ | $0\%$ | N/A *(Overdamped)* | — |
| **Part 1-2** | $10$ | $1.0$ | $(10,\; 10,\; 2.0)$ | $\frac{100}{s^2 + 20s + 100}$ | $0\%$ | N/A *(Critically Damped)* | — |
| **Part 1-3** | $10$ | $0.7$ | $(10,\; 10,\; 1.4)$ | $\frac{100}{s^2 + 14s + 100}$ | $4.5988\%$ | $0.44\text{ s}$ | $0.57\text{ s}$ |
| **Part 1-4** | $10$ | $0.2$ | $(10,\; 10,\; 0.4)$ | $\frac{100}{s^2 + 4s + 100}$ | $52.662\%$ | $0.32\text{ s}$ | $2.00\text{ s}$ |
| **Part 2-1** | $5$ | $0.2$ | $(10,\; 2.5,\; 0.2)$ | $\frac{25}{s^2 + 2s + 25}$ | $52.662\%$ | $0.64\text{ s}$ | $4.00\text{ s}$ |
| **Part 2-2** | $2$ | $0.2$ | $(1,\; 4.0,\; 0.8)$ | $\frac{4}{s^2 + 0.8s + 4}$ | $52.662\%$ | $1.60\text{ s}$ | $10.00\text{ s}$ |

---

## 4. Key Experimental Findings

1. **Invariance of Overshoot to $\omega_n$**: In Part 2, holding $\zeta = 0.2$ while reducing $\omega_n$ from $10 \to 5 \to 2\text{ rad/s}$ leaves the peak overshoot identical at $52.662\%$ across all three oscilloscope and Simulink waveforms, confirming that $\%OS$ is strictly a function of damping angle $\theta = \arccos(\zeta)$ in the $s$-plane.
2. **Time-Scaling Property of $\omega_n$**: Decreasing $\omega_n$ from $5\text{ rad/s}$ to $2\text{ rad/s}$ stretches the time axis by a factor of $5/2 = 2.5\times$, increasing $T_p$ from $0.64\text{ s}$ to $1.60\text{ s}$ and $T_s$ from $4.0\text{ s}$ to $10.0\text{ s}$.
3. **Engineering Trade-off in Damping Selection**: While decreasing $\zeta$ (e.g., $\zeta = 0.2$) slightly shortens the initial peak time $T_p$ ($0.32\text{ s}$ vs. $0.44\text{ s}$ at $\zeta = 0.7$), it induces severe ringing and increases total settling time $T_s$. A damping ratio around $\zeta \approx 0.7$ balances rapid rise with minimal overshoot ($< 5\%$).
