# Week 3 — LLM-as-Judge: Rubrics, Biases, Calibration, Trajectory Evals

**Theme:** go deep on the most powerful and most misused tool in AI evaluation —
using one model to grade another — and learn to prove your judge is trustworthy.

**Why this matters:** most quality dimensions that matter in production (helpfulness,
faithfulness, tone, reasoning quality) have no exact-match answer. LLM-as-judge is how
you grade them at scale — but an uncalibrated judge is just vibes with extra steps.
The professional move is a judge **calibrated against human labels**, with a reported
agreement score.

---

## Learning goals

By the end of this week you can:

- Write a bias-aware judge prompt with a concrete rubric.
- Name the major judge biases (position, length, self-preference, halo) and the
  mitigation for each.
- **Calibrate** a judge against your own human labels and report agreement.
- Grade agent **trajectories** (tool choice, order, arguments) — not just final answers.

---

## Tasks

### 1. Study judge biases until you can teach them (~1.5h)

- [ ] Read `docs/biases.md` and the judge-prompt template in
      https://github.com/wenxuec/llm-judge — a compact, practical treatment of
      rubric design, bias mitigation, and calibration.
- [ ] In `NOTES.md`, write one line per bias: what it is + your mitigation.
      Minimum set: position bias, length/verbosity bias, self-preference bias
      (a model favoring its own outputs — why your judge should come from a
      **different model family** than the system under test), halo effect,
      rubric drift.

### 2. Build a judge for a subjective task (~2h)

Task suggestion: grade **root-cause explanations** — given a scenario and a model's
diagnostic explanation, score correctness / evidence-use / clarity on a 1–5 rubric.

- [ ] Write the rubric first, as if for a human grader: what does a 2 look like vs
      a 4? Include worked examples ("anchors") for at least two score levels.
- [ ] Implement the judge with structured output (score + one-sentence reason),
      using your `JUDGE_MODEL` (different family from `CHAT_MODEL`).
- [ ] Randomize any pairwise comparisons (A/B position swap) — never grade in a
      fixed order.

### 3. Calibrate against yourself (~2.5h) — the heart of the week

- [ ] Generate 25 outputs from your Week 2 task (or the new one). **Hand-label all
      25 yourself first**, before running the judge. Don't skip this.
- [ ] Run the judge on the same 25. Compute agreement (exact-score agreement and
      within-1 agreement; for a pass/fail rubric, Cohen's kappa if you're feeling
      thorough).
- [ ] Analyze every disagreement: was the judge wrong, the rubric ambiguous, or your
      label wrong? Revise the rubric and re-run. Report before/after agreement.
- [ ] Write the calibration story in the README — this narrative ("judge agrees with
      human labels N% after rubric revision") is what makes an eval credible.

### 4. Trajectory evals for agents (~2h)

Final answers can be right for the wrong reasons; agents must also be graded on
**how** they got there.

- [ ] Take your Week 1 tool-calling agent. Log the full run: which tools were called,
      in what order, with what arguments.
- [ ] Write deterministic **gates**: "called `calculate` exactly once", "never called
      a tool after the final answer", "arguments were valid."
- [ ] Add a judge **scorer** over the whole trajectory: "did the agent take a
      sensible path; did it use tool results faithfully?"
- [ ] Explore DeepEval's agent metrics (tool correctness, task completion) for
      comparison: https://docs.confident-ai.com/

### 5. Survey the tooling landscape (read-only, ~1h)

- [ ] DeepEval: https://github.com/confident-ai/deepeval — pytest-style metrics
- [ ] Inspect AI: https://github.com/UKGovernmentBEIS/inspect_ai — scorer architecture
- [ ] eval-agents notebooks: https://github.com/VectorInstitute/eval-agents —
      agent evals with tracing (Langfuse) end to end
- [ ] In `NOTES.md`: which tool you'd pick for (a) a quick prompt regression,
      (b) a pytest suite, (c) a research-grade benchmark — and why.

---

## Resources

- llm-judge (rubrics, biases, calibration): https://github.com/wenxuec/llm-judge
- DeepEval docs: https://docs.confident-ai.com/
- Inspect AI docs: https://inspect.aisi.org.uk/
- eval-agents course: https://github.com/VectorInstitute/eval-agents
- "Who Validates the Validators?" (Shankar et al.) — the academic grounding for
  judge–human alignment: https://arxiv.org/abs/2404.12272

---

## Deliverable

A `judge/` module: rubric with anchors, bias-mitigated judge prompt, 25 human labels,
calibration script + before/after agreement numbers, and trajectory gates + scorer
for the Week 1 agent. The calibration README section is the showpiece.

## Self-check

1. Your judge agrees with you 95% of the time — on what dataset, and why might that
   number be misleading?
2. Why should the judge model come from a different family than the graded model?
3. Give an example where the final answer is correct but the trajectory should fail.
