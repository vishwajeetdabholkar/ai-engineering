# Week 6 — Capstone I: Architecture + Core Build

**Theme:** start the flagship project that ties every prior week together — a
multi-agent **incident-triage assistant** with retrieval, tools, guardrails, and
(next week) a full evaluation harness.

**Why this project:** it's customer-shaped (a realistic enterprise use case), it
exercises agents + RAG + structured output + escalation in one system, and its
diagnostic domain produces naturally gradeable outputs — perfect for the Week 7
eval harness. Swap the domain if you have a better-fitting one (legal intake,
claims triage, order-issue resolution) — the requirements below stay identical.

---

## The capstone spec

> **An AI incident-triage assistant.** Given an incident report ("checkout latency
> spiked at 14:00", "users can't upload files"), the system investigates using tools
> (querying mock metrics, logs, and a runbook knowledge base), narrows the root-cause
> category, and produces a **structured RCA draft** — or escalates to a human when
> confidence is low.

All on your Week 1 provider seam (Together AI by default; any OpenAI-compatible
provider identically).

---

## Tasks

### 1. Design before code (~2h)

- [ ] Write `docs/design.md` BEFORE building — one page:
      - Users and the decision the system changes (on-call engineer saves N minutes
        of manual investigation per incident).
      - Success criteria, measurable (triage accuracy, tool-path sanity, escalation
        precision, p95 latency, cost per incident).
      - Agent topology + tool inventory (draw it).
      - What's deliberately out of scope (auto-remediation — the system *recommends*).
- [ ] Have the discipline to keep this page honest as the build evolves.

### 2. Build the mock environment (~3h)

The realism of the capstone lives here — invest accordingly.

- [ ] `mock_metrics` API: per-service time series (latency, error rate, traffic,
      saturation) with seeded incident patterns — a latency spike, an error burst,
      a traffic cliff, a slow leak.
- [ ] `mock_logs` API: searchable log lines; each seeded incident plants consistent
      evidence across metrics AND logs (that cross-correlation is what the agent
      must discover).
- [ ] Runbook KB: 15–20 short markdown runbooks ("How we debug elevated 5xx",
      "Database connection pool exhaustion") — embedded into pgvector (Week 5).
- [ ] A scenario generator: `make_incident(kind)` produces a fresh incident with
      ground-truth root-cause category — this becomes the golden dataset factory
      for Week 7.

### 3. Build the agent system (~4h)

- [ ] **Triage agent**: reads the report, forms hypotheses, routes.
- [ ] **Specialist agents** (pick 3): e.g. Capacity/Load, Errors/Regression,
      Dependency/External — each with focused tool access.
- [ ] Tools: `query_metrics`, `search_logs`, `search_runbooks` (agentic RAG),
      `escalate_to_human(reason, confidence)`.
- [ ] **Structured final output** (Pydantic): root-cause category, confidence,
      evidence list (citing which metric/log/runbook), recommended next actions,
      escalation flag.
- [ ] Guardrails: input scope check; output check that every evidence item actually
      references a real tool result (no invented citations — enforce mechanically).
- [ ] FastAPI wrapper: `POST /triage` → structured RCA JSON.

### 4. First honest end-to-end pass (~1h)

- [ ] Run 5 generated incidents of different kinds. Read every trajectory fully.
- [ ] Write `NOTES.md`: where it impressed, where it flailed, which tool outputs it
      ignored. These observations seed Week 7's eval design — don't polish yet;
      **measure first, then improve.**

---

## Resources

- Everything from Weeks 4–5 (this is an integration week).
- Agents SDK patterns for agents-as-tools vs handoffs:
  https://openai.github.io/openai-agents-python/
- For mock-API inspiration, the "four golden signals" section of the Google SRE book:
  https://sre.google/sre-book/monitoring-distributed-systems/

---

## Deliverable

A running capstone: design doc, seeded mock environment with scenario generator,
multi-agent triage system with guardrails and structured RCA output, FastAPI endpoint,
and honest first-pass notes.

## Self-check

1. Which parts of your design doc changed during the build, and why?
2. How does the system distinguish "low confidence, escalate" from "confident but
   wrong"? (Hint: next week's eval harness is the real answer.)
3. If a stakeholder asked "why three specialists instead of one agent?", what's your
   evidence-based answer — or is it a hypothesis you should test?
