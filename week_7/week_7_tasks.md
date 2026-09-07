# Week 7 — Capstone II: Eval Harness, Observability, Cost Engineering

**Theme:** turn last week's working system into a **measured** system — golden
dataset, calibrated judge, CI regression suite, tracing, and a cost/latency analysis
across models. This is the week that separates the capstone from every other agent
demo on the internet.

---

## Learning goals

By the end of this week you can:

- Stand up a complete eval harness for a multi-agent system: dataset → gates →
  judge → regression → CI.
- Instrument an agent system with tracing and read trajectories like logs.
- Produce an honest **cost/latency/quality trade-off table** across model choices.
- Improve a system *because the evals said so*, and prove the improvement.

---

## Tasks

### 1. Golden dataset (~2h)

- [ ] Use the Week 6 scenario generator to produce **50 incidents** stratified across:
      every root-cause kind, difficulty tiers (single-signal vs cross-correlation),
      5 ambiguous cases (two plausible causes), and **5 insufficient-evidence cases
      where the correct behavior is to escalate**.
- [ ] Each case carries ground truth: root-cause category, key evidence items,
      expected escalation decision, and an acceptable-tool-path sketch.

### 2. Graders: gates + judge (~3h)

- [ ] **Deterministic gates** (from Week 3 skills):
      - final category matches ground truth,
      - escalation decision correct (report escalation **precision and recall**
        separately — over-escalation destroys the product's value too),
      - every cited evidence item exists in actual tool outputs (no fabrication),
      - tool-path sanity (queried metrics before concluding a capacity issue, etc.).
- [ ] **LLM-as-judge** for RCA quality: rubric with anchors (clarity, evidence
      quality, actionability), judge from a different model family, position-safe.
- [ ] **Calibrate**: hand-label 25 RCA drafts yourself first, run the judge, report
      agreement, revise rubric once, report again (the Week 3 workflow, for real).

### 3. Regression suite + CI (~1.5h)

- [ ] One command (`make eval`) runs all 50 cases and emits a scorecard:
      category accuracy, escalation precision/recall, fabrication count, judge-score
      distribution, mean tool calls, p50/p95 latency, cost per incident.
- [ ] Store scorecards per run (JSON in-repo or a small table) — the **history** is
      the artifact.
- [ ] CI: scripted-model tests (Week 4) on every PR; the full live eval on demand /
      nightly with a pass-rate threshold.

### 4. Observability (~2h)

- [ ] Add tracing with Langfuse (self-hosted or free tier):
      https://langfuse.com/docs — every run traced end to end: agent steps, tool
      calls with args/results, token counts, latencies.
- [ ] Reproduce one eval failure purely from its trace, without re-running.
      If you can't, your instrumentation has a gap — fix it.
- [ ] Structured logging (JSON) in the FastAPI layer: request ID, scenario kind,
      outcome, duration.

### 5. Cost & model engineering (~2.5h)

- [ ] Run the full suite against **three model configurations** (e.g. flagship model
      everywhere; small model for triage + flagship for specialists; small model
      everywhere). This is where a multi-model provider pays off.
- [ ] Produce the trade-off table: quality metrics × cost per incident × p95 latency,
      per configuration. Write a recommendation with reasoning.
- [ ] One optimization pass from the data (prompt trim, tool-output truncation,
      cheaper triage model…). Re-run. Show the before/after scorecard delta.

### 6. Eval-driven improvement loop (~1h)

- [ ] Pick the worst-performing case cluster from the scorecard. Diagnose from
      traces. Fix (prompt, tool description, guardrail, routing). Re-run. Document
      the loop in the README: *finding → hypothesis → change → measured result.*
      One well-documented loop is worth more than five undocumented fixes.

---

## Resources

- Langfuse: https://github.com/langfuse/langfuse · docs: https://langfuse.com/docs
- DeepEval (if you prefer pytest-style for the suite): https://docs.confident-ai.com/
- promptfoo CI patterns: https://www.promptfoo.dev/docs/integrations/github-action/
- Week 3's calibration references (llm-judge repo): https://github.com/wenxuec/llm-judge

---

## Deliverable

The measured capstone: 50-case golden dataset, gates + calibrated judge with reported
human-agreement, `make eval` scorecards with history, Langfuse tracing, the
three-model cost/quality/latency table with a recommendation, and one documented
eval-driven improvement loop.

## Self-check

1. Escalation recall is 100% and precision is 40%. What's happening, and is it bad?
2. Which of your metrics would you watch in production, and which only offline?
3. Your cheapest configuration loses 2 points of category accuracy but costs 8x less.
   Who should make that call, and what do they need from you to make it?
