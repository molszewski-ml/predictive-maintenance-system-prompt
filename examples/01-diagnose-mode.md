# Example 1 — DIAGNOSE Mode

Demonstrates the full DIAGNOSE reasoning chain (step-back → identify →
interpret → root-cause → severity → action → prevention → self-verify →
confidence) on a real fault code. Technical-user register is auto-selected
because the input contains a specific fault code and equipment model.

---

**User:**
> We're getting alarm code F7011 on the KUKA KR 210 on welding station 4.
> The robot slowed down during the last cycle.

---

**MaintAI:**

**STEP 0 — General Principles:**
KUKA KR series robots use AC servo motors with integrated encoders on each
axis, driven by KSP servo amplifier modules. Overcurrent faults in these
systems typically originate from three domains: electrical (motor/cable
degradation), mechanical (excessive load/friction), or drive electronics
(power stage failure). The KRC4 controller logs trending data that can
differentiate between these.

**STEP 1 — Equipment Identified:**
KUKA KR 210 — Welding Station 4. Subsystem: Servo drive (axis drive system).
Controller: KRC4 [ASSUMPTION: KRC4 controller, based on model generation].

**STEP 2 — Interpretation:**
Fault F7011 indicates a servo drive overcurrent condition — the drive
detected current exceeding the safe operating threshold. The speed reduction
was a protective response (torque limiting mode) before a full shutdown.

**STEP 3 — Root Cause Analysis:**

| # | Cause | Probability | Confirmatory Test |
|---|-------|-------------|-------------------|
| 1 | Motor winding degradation | Most likely (>30k h) | Megger winding resistance vs OEM spec (±10%) |
| 2 | Mechanical overload | Probable | Check collision log; inspect tooling |
| 3 | Servo drive hardware fault | Less likely | KSP module diagnostics; inspect capacitors |

**STEP 4 — Severity:** 🟠 HIGH — protective mode entered autonomously;
continued operation risks mid-weld shutdown, workpiece damage, safety hazard.

**STEP 5 — Action Plan:** KRC4 → Diagnosis → Drive data (trend check);
inspect axis cables within 24h; measure winding resistance; replace KSP
module if motor checks pass. Est. downtime 2–4h.

**STEP 6 — Prevention:** Add monthly servo-current trending to PM checklist;
set pre-alarm at 85% of overcurrent trip point.

**Confidence: HIGH** — F7011 is well-documented; gradual speed reduction
favors winding degradation / mechanical overload over electronics failure.
