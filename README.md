# Predictive Maintenance System Prompt — MaintAI

A production-grade system prompt that turns a general-purpose LLM into
**MaintAI**, a senior predictive-maintenance engineer for an automotive
assembly line. It interprets alarms and sensor readings, ranks maintenance
tasks, and plans shutdown windows with a hard safety layer that overrides
everything else.

This is a **prompt-only** repository: the design artifact itself, documented
and versioned. The running application that consumes it lives in
[`pdm-conversational-assistant`](https://github.com/molszewski-ml/pdm-conversational-assistant).

---

## Purpose

Off-the-shelf LLMs answer maintenance questions inconsistently; sometimes
verbose, sometimes unsafe, rarely in a format a technician can act on. This
prompt constrains the model into two disciplined modes and a fixed output
contract, so every response is structured, severity-rated, and safe by
default.

- **DIAGNOSE mode** — alarm / symptom / sensor-reading interpretation via an
  8-step reasoning chain ending in an explicit confidence statement.
- **PLAN mode** — maintenance scheduling and optimization via a
  scope → condition → priority-matrix → schedule pipeline.
- **Adaptive register** — infers technical vs non-technical audience from
  linguistic cues, no self-identification asked.
- **Absolute safety protocol** — immediate-danger response, LOTO enforcement,
  qualified-personnel gating, honest-uncertainty rule. These override all
  other instructions.

---

## Design choices

The prompt is engineered, not improvised. Each technique maps to a named
method catalogued in Schulhoff et al. (2025), *The Prompt Report*
([arXiv:2406.06608](https://arxiv.org/abs/2406.06608)).

| Technique in the prompt | Prompt Report method | Why it's here |
|---|---|---|
| "STEP 0 — Step Back" before diagnosing | **Step-Back Prompting** | Grounds reasoning in how the system *normally* works before pattern-matching a fault |
| Numbered 8-step DIAGNOSE chain | **Chain-of-Thought / structured reasoning** | Forces explicit intermediate steps instead of a jump to conclusion |
| Worked DIAGNOSE + PLAN interactions | **Few-Shot Prompting** | Two in-context exemplars fix the output format and depth |
| "STEP 7: Self-Verification" checklist | **Self-Verification / Self-Refine** | Model silently audits its own output before returning it |
| Confidence statement (HIGH/MODERATE/LOW) | **Confidence / calibration elicitation** | Surfaces uncertainty instead of hiding it, critical for safety calls |
| Fixed severity badges 🔴🟠🟡🟢 | **Constrained answer space** | One of four defined labels, never free-form severity |
| Multi-system → per-asset breakdown | **Decomposition** | Splits complex faults into independent sub-diagnoses, then synthesizes |
| Role + stakes framing (20y engineer, €/h cost) | **Role / persona prompting** | Sets expertise level and the seriousness of the task |

Details on each technique:
[Prompt Report taxonomy](https://arxiv.org/abs/2406.06608) ·
[Step-Back prompting](https://www.google.com/search?q=step-back+prompting+technique+LLM) ·
[Self-verification in LLMs](https://www.google.com/search?q=self+verification+self+refine+LLM+prompting)

---

## Repository structure

```
predictive-maintenance-system-prompt/
├── system-prompt.md      # the prompt itself (source of truth)
├── examples/
│   ├── 01-diagnose-mode.md
│   └── 02-plan-mode.md
├── docs/                 # assignment brief (assignment-requirements.pdf)
├── LICENSE               # CC BY 4.0
└── README.md
```

---

## How to use

1. Copy the contents of [`system-prompt.md`](./system-prompt.md).
2. Set it as the **system message** of any capable chat model (tested with
   `qwen3.5:9b` via [Ollama](https://ollama.com/library/qwen3.5:9b)).
3. Send an alarm, symptom, or planning request as the user message.

Minimal Ollama example:

```python
import ollama

with open("system-prompt.md") as f:
    system_prompt = f.read()

response = ollama.chat(
    model="qwen3.5:9b",
    messages=[
        {"role": "system", "content": system_prompt},
        {"role": "user",   "content": "Vibration on pump P-102 jumped from 2.1 to 6.8 mm/s RMS over two days."},
    ],
)
print(response["message"]["content"])
```

See [`examples/`](./examples) for full DIAGNOSE and PLAN walkthroughs.

---

## In production

This prompt is the design artifact. The deployable Gradio / terminal
application that loads it, streams responses, and tests it against real
sensor data (AI4I 2020 Predictive Maintenance dataset) is here:

➡️ **[pdm-conversational-assistant](https://github.com/molszewski-ml/pdm-conversational-assistant)**

---

## License

The prompt and documentation are licensed under
[**CC BY 4.0**](https://creativecommons.org/licenses/by/4.0/) — free to use,
adapt, and build on, including commercially, with attribution. See
[`LICENSE`](./LICENSE).
