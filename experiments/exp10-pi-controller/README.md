# Experiment 10: Proportional-Integral (PI) Controller Analysis

* **Original Report**: [`控制實習報告10_PI控制器.pdf`](./控制實習報告10_PI控制器.pdf)
* **Course**: Control Systems Laboratory (Class 3A, Group 12)
* **Authors**: Hsing-Cheng Liu (`0116C001`), Yi-Wei Su (`0116C031`)

---

## 1. Objective

Analyze the interplay between **Proportional ($K_P$)** and **Integral ($K_I$)** control actions on first-order (DC servo speed) and second-order (DC servo position) plants. Specifically, demonstrate how integral control eliminates steady-state tracking error by incrementing the System Type Number, and how proportional gain stabilizes the closed-loop root locus when integral action alone drives a second-order plant into third-order instability.

> **Documentation Note on the Original PDF**: In `控制實習報告10_PI控制器.pdf`, the introductory background paragraphs (Sections 1–3, pp. 1–3) retain boilerplate text from the D-controller lab template. The actual **PI Controller experimental results, oscilloscope captures, Simulink simulations, root-locus diagrams, and 3rd-order characteristic root analyses** are fully presented in Section 3 (pp. 4–6) and Section 4 (pp. 6–11).

---

## 2. Experimental Matrix

### Part 1: Closed-Loop Speed Control (First-Order Plant)
* **Case 1(1) — Fixed $K_P = 1$**: Varied $K_I \in \{0,\; 2,\; 10\}$.
* **Case 1(2) — Fixed $K_I = 100$**: Varied $K_P \in \{0,\; 0.5,\; 1\}$ (Simulink also compared $K_P = 10$).

### Part 2: Closed-Loop Position Control (Second-Order Plant)
* **Case 2(1) — Fixed $K_P = 1$**: Varied $K_I \in \{0,\; 1,\; 5\}$.
* **Case 2(2) — Fixed $K_I = 1$**: Varied $K_P \in \{0,\; 0.5,\; 1\}$ (Simulink also compared $K_P = 0.05$).

---

## 3. Theoretical & Root Locus Analysis

### 3.1 PI Control on a First-Order Plant
Adding a PI controller $C(s) = K_P + \frac{K_I}{s}$ to a first-order plant $G_p(s) = \frac{1}{s + P_1}$ elevates the system from 1st-order Type 0 to **2nd-order Type 1**:

$$M(s) = \frac{K_P s + K_I}{s^2 + (K_P + P_1)s + K_I} \quad \Longrightarrow \quad \omega_n = \sqrt{K_I}, \qquad \zeta = \frac{K_P + P_1}{2\sqrt{K_I}}$$

* **Zero Steady-State Error**: Unlike P-only control (where $e_{ss} = \frac{1}{1+K_P} > 0$), the integral pole at $s=0$ drives step steady-state error strictly to zero ($e_{ss} = 0$).
* **Complementary Roles of $K_I$ and $K_P$**: Increasing $K_I$ raises $\omega_n = \sqrt{K_I}$ (faster response) but reduces $\zeta$, inducing overshoot when $\zeta < 1$. Increasing $K_P$ directly increases the damping term $(K_P + P_1)$ without altering $\omega_n$:
  * In the fixed $K_I = 100$ speed-control model $M(s) = \frac{50 K_P s + 5000}{s^2 + (50 + 50 K_P)s + 5000}$ ($\omega_n = \sqrt{5000}$):
    * $K_P = 0$ (I-only): $\zeta = 0.3535$ (pronounced overshoot)
    * $K_P = 0.5$: $\zeta = 0.530$
    * $K_P = 1.0$: $\zeta = 0.707$ (well-damped response)
    * $K_P = 10.0$: $\zeta = 3.889$ (overdamped, zero overshoot)

---

### 3.2 PI Control on a Second-Order Plant (3rd-Order Root Locus & RHP Instability)
Applying PI control to a 2nd-order plant $G_p(s) = \frac{b_0}{s^2 + a_1 s + a_0}$ produces a **3rd-order closed-loop characteristic equation**:

$$s^3 + a_1 s^2 + (a_0 + K_P b_0)s + K_I b_0 = 0$$

1. **Instability under Pure Integral Control ($K_P = 0$)**:
   For the lab model $s^3 + 50s^2 + 50 K_I = 0$ (missing the $s^1$ term when $a_0 = 0, K_P = 0$), Routh–Hurwitz and numerical root solving (computed via Python in Table 9 of the report) confirm that two complex conjugate poles lie in the **Right-Half Plane (RHP)** for all $K_I > 0$:
   * $K_I = 1$: Roots at $-50.020$ and $+0.010 \pm 0.999i$ *(Unstable)*
   * $K_I = 2$: Roots at $-50.040$ and $+0.020 \pm 1.414i$ *(Unstable)*
   * $K_I = 5$: Roots at $-50.100$ and $+0.050 \pm 2.233i$ *(Unstable)*
   * $K_I = 10$: Roots at $-50.198$ and $+0.099 \pm 3.154i$ *(Unstable)*
2. **Stabilization via Proportional Gain ($K_P > 0$)**:
   Introducing the PI zero ($K_P s + K_I$) restores the $s^1$ coefficient in $s^3 + 50s^2 + 50 K_P s + 250 = 0$ (at fixed $K_I = 1$, Table 11), pulling the root-locus branches back into the Left-Half Plane:
   * $K_P = 0.05$: Roots at $-50.050$ and $+0.025 \pm 2.235i$ *(Still in RHP — Unstable)*
   * $K_P = 0.50$: Roots at $-49.598$ and $-0.201 \pm 2.236i$ *(Stable, lightly damped oscillation)*
   * $K_P = 1.00$: Roots at $-49.085$ and $-0.457 \pm 2.210i$ *(Stable, improved damping and reduced overshoot)*
