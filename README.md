# Agents-A1 vs Qwen3.6-35B — Tool-Eval Bench Comparison

Head-to-head comparison of two GGUF agent models on **[tool-eval-bench](https://github.com/SeraphimSerapis/tool-eval-bench) v2.0.6** — 84 tool-calling scenarios, 8 independent trials each, scored pass / partial / fail.

**[View interactive report →](Agents-A1_vs_Qwen3.6-35B-A3B-UD-Q8_K_XL.html)**

---

## Models compared

These are the exact GGUF artifacts loaded in vLLM on `spark1`:

| | GGUF file | Quantization | vLLM model name |
|---|---|---|---|
| **Qwen3.6-35B** | `Qwen3.6-35B-A3B-UD-Q8_K_XL.gguf` | Unsloth Dynamic (**UD**), **Q8_K_XL** | `Qwen3.6-35B-A3B-UD-Q8_K_XL` |
| **Agents-A1** | `Agents-A1-Q8_0.gguf` | **Q8_0** | `Agents-A1-Q8_0.gguf` |

> **Note on Agents-A1 quantization:** `Agents-A1-Q8_0.gguf` was the **highest-quality quant available for Agents-A1 at the time of this evaluation** (July 2026). No higher-bit or UD variant was published yet, so this comparison uses the best reproducible Agents-A1 checkpoint — not a deliberately weakened build.

Qwen3.6 was benchmarked with the corresponding **UD-Q8_K_XL** release (June 2026), which is the standard high-quality GGUF distribution for that model family.

---

## Verdict

**Winner: [Qwen3.6-35B-A3B-UD-Q8_K_XL](Qwen3.6-35B-A3B-UD-Q8_K_XL_summary.md)** — higher mean score, better reliability floor, faster median turn time, and fewer safety warnings.

| | Qwen3.6-35B-A3B-UD-Q8_K_XL | Agents-A1-Q8_0.gguf |
|---|---|---|
| **Mean score** | **91.0** ± 1.5 | 83.4 ± 1.5 |
| **Mean points** | **152.9** / 168 | 140.1 / 168 |
| **Rating** | ★★★★ Good | ★★★★ Good |
| **Pass@8** (ceiling) | **91.7%** | 90.5% |
| **Pass^8** (floor) | **76.2%** | 64.3% |
| **Reliability gap** | **15.5 pp** | 26.2 pp |
| **Deployability** (α=0.7) | **79** / 100 | 73 / 100 |
| **Median turn** | **2.5 s** | 3.6 s |
| **Safety warnings** (max/trial) | **1** | 3 |
| **Never-pass scenarios** | **0** | 3 |

Both models were evaluated on the same host (`spark1`) via vLLM with thinking enabled. Temperature differed: Qwen3.6 at **1.0**, Agents-A1 at **0.85** with `top_p=0.95`.

---

## Highlights

### Qwen3.6-35B strengths

- **+7.6** mean score over Agents-A1
- **100%** on tool selection, multi-step chains, error recovery, and structured output (trial averages)
- **0** never-pass scenarios vs 3 for Agents-A1
- Passes TC-31 (ambiguity resolution) and TC-61 (async polling) reliably — both are hard fails for Agents-A1

### Agents-A1 strengths

- **100%** restraint & refusal (vs 98% for Qwen3.6)
- Slightly higher creative composition (**85%** vs 83%)
- Still a solid ★★★★ Good rating at 83.4 mean — competitive, but less consistent across trials

### Shared weak spots

Both models struggle with long-horizon workflows: TC-46 (multi-phase research), TC-84 (booking + email pipeline), and related context/state scenarios show consistent partial results.

---

## Files in this repo

| File | Description |
|---|---|
| [`Agents-A1_vs_Qwen3.6-35B-A3B-UD-Q8_K_XL.html`](Agents-A1_vs_Qwen3.6-35B-A3B-UD-Q8_K_XL.html) | Interactive comparison report (open in browser) |
| [`Agents-A1-Q8_0_summary.md`](Agents-A1-Q8_0_summary.md) | Cross-trial summary — Agents-A1 (8 trials, Jul 2026) |
| [`Qwen3.6-35B-A3B-UD-Q8_K_XL_summary.md`](Qwen3.6-35B-A3B-UD-Q8_K_XL_summary.md) | Cross-trial summary — Qwen3.6 (8 trials, Jun 2026) |

---

## Methodology

Benchmark: **[tool-eval-bench](https://github.com/SeraphimSerapis/tool-eval-bench)** v2.0.6 (`f8117c3`)

- **84 scenarios** covering tool selection, parameter precision, multi-step chains, safety/injection resistance, structured output, and hard-mode adversarial cases (TC-70 – TC-84)
- **8 trials** per model with fixed seed (42), max 8 turns, 60 s timeout, sequential execution
- **3-tier scoring**: pass (2 pts), partial (1 pt), fail (0 pts) — max 168 points
- **Reliability metrics**:
  - **Pass@k** — % of scenarios that pass in at least one trial (capability ceiling)
  - **Pass^k** — % of scenarios that pass in every trial (reliability floor)
- **Deployability** — `0.7 × quality + 0.3 × responsiveness` (median turn latency factored in)

Full per-scenario traces and category breakdowns are in the summary markdown files linked above.

---

## Viewing locally

Clone this folder and open the HTML report:

```bash
git clone <your-repo-url>
cd <repo>
xdg-open Agents-A1_vs_Qwen3.6-35B-A3B-UD-Q8_K_XL.html   # Linux
open Agents-A1_vs_Qwen3.6-35B-A3B-UD-Q8_K_XL.html       # macOS
```

### GitHub Pages

To publish the report at a public URL, enable **GitHub Pages** on this repo (branch: `main`, folder: `/ (root)`). The comparison HTML will be served at:

```
https://<user>.github.io/<repo>/Agents-A1_vs_Qwen3.6-35B-A3B-UD-Q8_K_XL.html
```

---

## Run dates

| GGUF file | Run period | Trials |
|---|---|---|
| `Qwen3.6-35B-A3B-UD-Q8_K_XL.gguf` | 2026-06-28 | 8 |
| `Agents-A1-Q8_0.gguf` | 2026-07-01 | 8 |

---

## License & attribution

Benchmark framework: [tool-eval-bench](https://github.com/SeraphimSerapis/tool-eval-bench) — see that repository for license terms.

Comparison report and summary artifacts in this folder are published for reproducibility and model evaluation reference.