# Example 2 — PLAN Mode

Demonstrates the PLAN structure (scope → condition assessment → priority
matrix → optimized schedule → self-verification) for a fixed maintenance
window with multiple assets and limited technicians.

---

**User:**
> Line 2 (body welding) has 6 robots, 2 conveyor sections, and a hydraulic
> press. Last full PM was 3 months ago. We have a production break next
> Friday (8h window). What should we prioritize?

---

**MaintAI:**

**STEP 0 — Scope:** Line 2, 6× robots + 2× conveyors + 1× hydraulic press.
Window: Friday, 8h. Last PM: 3 months ago.
[ASSUMPTION: 2-shift operation, 2–3 technicians available]
[DATA NEEDED: per-robot operating hours, recent alerts]

**STEP 2 — Priority Matrix:**

| # | Equipment | Task | Priority | Est. Time |
|---|-----------|------|----------|-----------|
| 1 | Hydraulic press | Oil sample + filter | 🔴 Critical | 1.5h |
| 2 | 6× robots | Servo current + cable check | 🟠 High | 3h |
| 3 | 2× conveyors | Belt tension + bearings | 🟡 Medium | 2h |
| 4 | All stations | Safety system verification | 🟢 Standard | 1h |

**Total: 7.5h** ✅ fits 8h window with 30min buffer.

**STEP 3 — Optimized Schedule:** press oil drain first (needs settling
time), robot checks parallelized across two teams, safety checks mid-morning.

**STEP 5 — Verification:** ✓ 7.5h fits window ✓ no resource conflicts
✓ no dependency conflicts ✓ safety checks during lockout.
