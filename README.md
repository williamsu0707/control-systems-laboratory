# Control Systems Laboratory Portfolio

An undergraduate control systems engineering portfolio documenting nine hands-on hardware and simulation laboratories completed in the **Department of Communications, Navigation and Control Engineering** (Class 3A, Group 12 — Yi-Wei Su `0116C031` & Hsing-Cheng Liu `0116C001`).

Each experiment bridges **analytical control theory**, **MATLAB/Simulink numerical simulation**, and **real-time analog/electromechanical hardware verification** using modular analog control blocks (`CM01`–`CM10`), a DC servo motor plant with tachometer and potentiometer feedback, and a digital storage oscilloscope (`GW Instek GDS-1102B` / `GDS-2102`).

---

## Repository Structure

```text
control-systems-laboratory/
├── README.md
└── experiments/
    ├── exp02-system-modeling/
    │   ├── README.md
    │   ├── 控制實習報告2.pdf
    │   └── simulink/
    ├── exp03-steady-state-error/
    │   ├── README.md
    │   ├── 控制實習報告3.pdf
    │   └── simulink/
    ├── exp04-second-order-response/
    │   ├── README.md
    │   ├── 控制實習報告4.1.pdf
    │   └── simulink/
    ├── exp05-transient-specifications/
    │   ├── README.md
    │   ├── 控制實習報告5.pdf
    │   └── simulink/
    ├── exp07-p-controller-dc-servo/
    │   ├── README.md
    │   ├── 控制實習報告7.pdf
    │   └── simulink/
    ├── exp10-pi-controller/
    │   ├── README.md
    │   ├── 控制實習報告10_PI控制器.pdf
    │   └── simulink/
    ├── exp11-pd-controller/
    │   ├── README.md
    │   ├── 控制實習報告11_PD控制器.pdf
    │   └── simulink/
    ├── exp12-pid-ziegler-nichols/
    │   ├── README.md
    │   ├── 控制實習報告12_PID控制器實驗.pdf
    │   └── simulink/
    └── exp29-state-feedback-pole-placement/
        ├── README.md
        ├── 控制實習期末實驗報告.pdf
        └── simulink/
```

> **Note on Original Reports & Simulink Files:** Each experiment directory contains the unmodified original Chinese laboratory report PDF (including all oscilloscope captures, Simulink block diagrams, and analytical derivations) alongside a comprehensive English `README.md`. The `simulink/` subdirectories are left intentionally empty as placeholders because the original `.slx` workspace files were not archived separately from the laboratory computers.

---

## Laboratory Index & Technical Summary

| Lab | Directory | Topic | Core Control Concepts | Plant / Hardware Modules |
| :--- | :--- | :--- | :--- | :--- |
| **Lab 02** | [`exp02-system-modeling`](./experiments/exp02-system-modeling/) | System Modeling & Damping Analysis | Homogeneous vs. forced ODE solutions; overdamped ($\zeta > 1$), critically damped ($\zeta = 1$), and underdamped ($0 < \zeta < 1$) regimes; parameter sensitivity to $m, c, k$ | Analog integrators (`CM06-1`, `CM06-2`), feedback gains (`CM05`, `CM07`), `CM10` step source |
| **Lab 03** | [`exp03-steady-state-error`](./experiments/exp03-steady-state-error/) | Steady-State Error Analysis | System Type Number (Type 0, Type 1, Type 2); position ($K_p$), velocity ($K_v$), and acceleration ($K_a$) error constants under Step, Ramp, and Parabolic inputs | `CM01` error detector, `CM06` integrators, `CM08` plant, `CM09` lead-lag network |
| **Lab 04** | [`exp04-second-order-response`](./experiments/exp04-second-order-response/) | Second-Order System Time Response | Standard 2nd-order dynamics $G(s) = rac{\omega_n^2}{s^2 + 2\zeta\omega_n s + \omega_n^2}$; independent variation of damping ratio $\zeta$ and natural frequency $\omega_n$; peak time $T_p$ and maximum overshoot $\%OS$ | `CM08` Second-Order Plant, `CM01` summing junction, `CM10` step generator |
| **Lab 05** | [`exp05-transient-specifications`](./experiments/exp05-transient-specifications/) | Transient Response Specifications | Quantitative evaluation of delay time ($T_d$), rise time ($T_r$), peak time ($T_p$), settling time ($T_s$), and maximum overshoot ($M_p$) | `CM08` Second-Order Plant, `CM01`, `CM10`, oscilloscope cursor measurement |
| **Lab 07** | [`exp07-p-controller-dc-servo`](./experiments/exp07-p-controller-dc-servo/) | Proportional (P) Control of DC Servo Motor | Closed-loop DC servo speed control (1st-order Type 0) vs. angular position control (2nd-order Type 1); root locus pole migration with $K_P$ | `CM02` ($K_P$), Servo Amplifier, DC Servo Motor, Tachometer ($V_\omega$), Potentiometer ($V_\theta$) |
| **Lab 10** | [`exp10-pi-controller`](./experiments/exp10-pi-controller/) | Proportional-Integral (PI) Control | System order elevation via integral action; elimination of steady-state error; RHP pole instability under pure I control on 2nd-order plants and stabilization via $K_P$ | `CM02` ($K_P$), `CM03` ($K_I/s$), `CM05` summer, `CM08` / DC Servo Motor |
| **Lab 11** | [`exp11-pd-controller`](./experiments/exp11-pd-controller/) | Proportional-Derivative (PD) Control | Anticipatory damping via derivative action ($K_D s$); effective damping increase without raising system order; transient stabilization at high $K_P$ | `CM02` ($K_P$), `CM04` ($K_D s$), `CM05` summer, `CM08` / DC Servo Motor |
| **Lab 12** | [`exp12-pid-ziegler-nichols`](./experiments/exp12-pid-ziegler-nichols/) | PID Tuning with Ziegler–Nichols Rules | 1st Method (open-loop S-curve reaction curve $	au_d, 	au$) vs. 2nd Method (closed-loop ultimate gain $K_u$ and period $P_u$); practical hardware fine-tuning | `CM02` (`P`), `CM03` (`I`), `CM04` (`D`), `CM05`, `CM06`, `CM08`, `CM09` |
| **Final (Lab 29)** | [`exp29-state-feedback-pole-placement`](./experiments/exp29-state-feedback-pole-placement/) | State Feedback & Pole Placement | State-space modeling of DC servo motor; full-state feedback $u = -K\mathbf{x} + r$; exact closed-loop pole assignment across 5 $(\omega_n, \zeta)$ specifications | MATLAB (`place` / `acker`), Simulink state-feedback architecture |

---

## Hardware & Software Environment

* **Analog Control Modules**:
  * `CM01`: Error Detector / Summing Junction ($V_o = V_{i1} - V_{i2}$)
  * `CM02`: Proportional Controller ($K_P$)
  * `CM03`: Integral Controller ($K_I / s$)
  * `CM04`: Derivative Controller ($K_D s$)
  * `CM05`: Weighted Summer / Gain Block ($K$)
  * `CM06-1` / `CM06-2`: Integrator Modules with Initial Condition (`IC`) & `OP/RESET` Mode Switches
  * `CM07`: Dual Feedback Gain Module ($-K_1, -K_2$)
  * `CM08`: Configurable First/Second-Order Plant Module
  * `CM09`: Phase Lead / Lag Compensator Module
  * `CM10`: Low-Frequency Step / Square-Wave Reference Generator
* **Electromechanical Plant**: DC Servo Motor unit with PWM Servo Amplifier, cooling fan, Tachometer generator (velocity feedback $V_\omega$), and worm-gear Potentiometer (angular position feedback $V_\theta$).
* **Instrumentation**: GW Instek `GDS-1102B` / `GDS-2102` Digital Storage Oscilloscope.
* **Simulation Suite**: MATLAB & Simulink.
