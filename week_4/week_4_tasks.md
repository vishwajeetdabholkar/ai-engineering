# Week 4 — Agents: Tools, Handoffs, Guardrails, Deterministic Testing

**Theme:** build real multi-agent systems — triage → specialists, with guardrails —
and learn to test them deterministically, without burning tokens or trusting luck.

**Why this matters:** the dominant production agent pattern is not one giant agent;
it's a **triage agent routing to focused specialists**, each with a small tool set and
tight instructions. And unlike most people, you'll be able to answer "how do you test
an agent?" with something better than "we run it and look."

---

## Learning goals

By the end of this week you can:

- Design a multi-agent topology: when to use handoffs vs one agent with many tools.
- Implement input/output **guardrails** and human-escalation paths.
- Manage context and sessions across a conversation.
- Write **deterministic agent tests** with scripted models — zero API calls.

---

## Tasks

### 1. Study the reference implementation (~2h)

- [ ] Read (don't run yet) OpenAI's customer-service multi-agent demo:
      https://github.com/openai/openai-cs-agents-demo
      — triage agent, specialist agents, guardrails, and a UI. Sketch its agent
      graph on paper: who hands off to whom, and what each agent can touch.
- [ ] Skim a production-hardened variant of the same idea for the ops layer
      (health endpoints, metrics, e2e tests with mocked deps):
      https://github.com/DIZ-admin/openai-cs-agents
- [ ] Skim two community builds for architectural contrast:
      https://github.com/jawwad-ali/ai-customer-support-agent (semantic KB search,
      escalation) and
      https://github.com/muhammadwaheedairi/Multi-Channel-Customer-Success-Agent
      (channel routing, structured logging).

### 2. Rebuild from first principles (~4h) — the core exercise

Close the reference repos. Build your own **triage → specialists** system with the
Agents SDK on your provider seam from Week 1. Domain suggestion: an internal IT
helpdesk (access requests, hardware issues, software bugs, how-to questions).

- [ ] One **triage agent** whose only job is routing (no answering).
- [ ] Three **specialist agents**, each with 1–3 focused function tools
      (stub the tools with realistic fake data).
- [ ] **Handoffs** wired triage → specialists, plus one specialist → specialist
      handoff (e.g. hardware → access when a device swap needs new credentials).
- [ ] An **input guardrail** (reject/deflect off-topic or abusive input) and an
      **output guardrail** (block responses that leak internal identifiers).
- [ ] An **escalate_to_human** tool available to every specialist, triggered on
      low confidence or explicit user request.
- [ ] Session memory: the user shouldn't repeat themselves across turns.

Only after finishing, reopen the reference demo and write a comparison in `NOTES.md`:
what they did better, what you did better.

### 3. Deterministic testing (~2h)

- [ ] Read the SDK testing docs: https://openai.github.io/openai-agents-python/testing/
- [ ] Write tests using `ScriptedModel` (no network, no tokens) covering:
      - triage routes each category to the right specialist,
      - a guardrail actually blocks a bad input,
      - the escalation tool fires on the scripted low-confidence path,
      - a two-hop handoff preserves context.
- [ ] Add the trajectory gates from Week 3 as assertions on live runs (one small
      live smoke suite, run manually; the scripted suite runs in CI).

### 4. Failure-mode drill (~1h)

- [ ] Break things on purpose and observe: a tool that raises, a tool that returns
      garbage JSON, a model that loops calling the same tool. For each, decide and
      implement the policy (retry? surface to user? escalate?). Document in `NOTES.md`.

---

## Resources

- Agents SDK docs (all sections): https://openai.github.io/openai-agents-python/
- SDK examples directory: https://github.com/openai/openai-agents-python/tree/main/examples
- OpenAI's "A Practical Guide to Building Agents" (PDF, short and dense):
  https://cdn.openai.com/business-guides-and-resources/a-practical-guide-to-building-agents.pdf
- Anthropic's "Building Effective Agents" — the best essay on when NOT to use
  multi-agent complexity: https://www.anthropic.com/research/building-effective-agents

---

## Deliverable

Your helpdesk multi-agent system: 4 agents, guardrails, escalation, sessions,
a `ScriptedModel` test suite in CI, and a failure-mode policy writeup.

## Self-check

1. When is a single agent with 10 tools better than 4 agents with handoffs?
2. What state must survive a handoff, and what should be deliberately dropped?
3. Why are scripted-model tests necessary but not sufficient? What do they miss?
