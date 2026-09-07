# Week 2 — Evaluation Fundamentals: Datasets, Graders, Regression Runs

**Theme:** learn the core discipline of applied AI — measuring whether an LLM system
actually works — before building anything bigger.

**Why this matters:** the difference between a demo and a production system is that
the production system can answer "how good is it, on what data, and did the last
change make it better or worse?" Evaluation is that answer. We learn it early so
every later build is measurable by design.

---

## Learning goals

By the end of this week you can:

- Design a **golden dataset**: small, stratified, representative — and explain why
  50 well-chosen cases beat 500 random ones.
- Choose the right **grader** for a task: exact match → programmatic checks →
  model-graded rubric → human review.
- Run **regression evals**: change a prompt, re-run the suite, diff the results.
- Wire evals into **CI** so no prompt change lands unmeasured.

---

## Tasks

### 1. Install promptfoo and run the smoke test (~1h)

- [ ] `npm install -g promptfoo`, then `promptfoo init` in a `week_2/eval-playground/` dir.
- [ ] Point it at your provider (promptfoo supports OpenAI-compatible endpoints and
      has a native Together provider): https://www.promptfoo.dev/docs/providers/togetherai/
- [ ] Run the sample eval, open the web viewer (`promptfoo view`), understand the
      pass/fail matrix.

### 2. Build a real task + golden dataset (~2.5h)

Pick one concrete, gradeable task. Recommended: **support-ticket triage** —
classify a ticket into one of 6 categories + urgency + one-line summary.
(You'll reuse this domain in the capstone.)

- [ ] Author **30 synthetic tickets** covering: easy cases, ambiguous cases,
      multi-issue tickets, angry-tone tickets, non-English fragments, and
      red herrings (mentions billing but is actually a bug report).
- [ ] For each, record the expected category and urgency in a CSV/YAML dataset.
- [ ] Document your **stratification logic** in the README: what case types exist,
      why, and roughly what share of each. This reasoning is the skill.

### 3. Write graders across the whole taxonomy (~2h)

- [ ] **Deterministic:** `equals` on category, `javascript`/`python` assertion on
      urgency rules ("anything mentioning data loss is at least High").
- [ ] **Model-graded:** an `llm-rubric` assertion for summary quality
      ("captures the core issue; ≤ 20 words; no invented details").
- [ ] Run the full suite against **two different models** from your provider and
      compare pass rates. Note where the models disagree — those cases are gold
      for dataset improvement.

### 4. The regression workflow (~1.5h)

- [ ] Deliberately "improve" your prompt (add few-shot examples). Re-run. Diff.
- [ ] Now deliberately break it subtly (remove one category from the instructions).
      Confirm the suite catches it. If it doesn't, your dataset has a coverage hole —
      fix the dataset, not just the prompt.
- [ ] Save both result runs; write 5 lines in `NOTES.md` on what the diff view showed.

### 5. Evals in CI (~1h)

- [ ] Add a GitHub Action that runs `promptfoo eval` on every PR that touches
      `prompts/` or the dataset, and fails the build if the pass rate drops below
      a threshold. Reference: https://www.promptfoo.dev/docs/integrations/github-action/

---

## Resources

- promptfoo: https://github.com/promptfoo/promptfoo · docs: https://www.promptfoo.dev/docs/intro/
- OpenAI Cookbook — evals section: https://github.com/openai/openai-cookbook
  (search "eval"; read the LLM-evaluation guides for methodology, even where the
  hosted tooling is provider-specific)
- Inspect AI docs — read the **concepts** pages for the cleanest dataset → solver →
  scorer mental model in the ecosystem: https://inspect.aisi.org.uk/
- Hamel Husain, "Your AI Product Needs Evals": https://hamel.dev/blog/posts/evals/
  (the single best essay on this topic)

---

## Deliverable

`eval-playground/`: task prompt(s), 30-case stratified dataset, grader suite spanning
deterministic + model-graded assertions, two-model comparison results, and a CI
workflow. Push it public — it's small, but it demonstrates the full loop.

## Self-check

1. When is exact-match grading wrong even when it's possible?
2. Your pass rate went from 87% → 90% after a prompt change. What must you check
   before celebrating?
3. Why stratify a dataset instead of sampling randomly from real traffic?
