# Week 8 — Production Polish: Docs, Portability, Presentation

**Theme:** finish like a professional — harden the capstone, prove provider
portability, write documentation for a non-engineering audience, and present the
work in a recorded walkthrough. Communication is the second half of applied AI
engineering; this week trains it deliberately.

---

## Learning goals

By the end of this week you can:

- Package an AI system so a stranger can run it in 10 minutes.
- Swap LLM providers and **prove** nothing broke (with the eval suite, not vibes).
- Write a README that leads with the problem and the measured results, not the stack.
- Present a technical build to a mixed audience in 10 minutes, on camera.

---

## Tasks

### 1. Operational hardening (~2.5h)

- [ ] Dockerize: multi-stage build, `docker compose up` brings up API + Postgres/
      pgvector (+ Langfuse if self-hosting). One command, fresh machine, working system.
- [ ] `/health` and `/readiness` endpoints; graceful handling of provider timeouts
      and rate limits (retry with backoff + a circuit-breaker style give-up path
      that returns a clean error, never a hang).
- [ ] Config via environment only; `.env.example` complete; secrets never in git
      history (check!).
- [ ] A `make demo` target that seeds an incident and pretty-prints the RCA —
      the 30-second wow path for anyone who clones the repo.

### 2. The portability proof (~2h)

The provider seam from Week 1 gets its payoff.

- [ ] Swap providers by changing only `LLM_BASE_URL` / model names (e.g. Together AI
      → OpenAI, or → any other OpenAI-compatible endpoint, or a local server like
      vLLM/Ollama for the smaller models).
- [ ] Re-run the full eval suite on the second provider. Publish both scorecards
      side by side in the README. Differences are not failure — **explaining them
      is the skill** (tool-calling reliability, JSON adherence, latency profile).
- [ ] Document any portability potholes you hit (schema support differences,
      tool-call format quirks) in `docs/portability.md` — genuinely useful public
      content that very few people write down.

### 3. The README rewrite (~2h)

Rewrite the capstone README **for a technical decision-maker**, in this order:

1. The problem and who has it (two sentences).
2. What the system does — with the `make demo` output as the hook.
3. **Results table** — the Week 7 scorecard: accuracy, escalation precision/recall,
   judge quality with human-agreement, cost/latency per configuration.
4. Architecture (one diagram — Mermaid renders on GitHub).
5. Evaluation methodology — dataset design, gates + calibrated judge, CI. This
   section is your differentiator; most public agent projects have nothing here.
6. Honest limitations + "what I'd do at 100× scale."
7. Quickstart.

- [ ] Also add 2–3 paragraphs to the top-level path README reflecting on the 8 weeks:
      what surprised you, what you'd reorder.

### 4. The recorded walkthrough (~2.5h)

- [ ] Record a **10-minute video** presenting the capstone as if to a client's
      engineering leadership: the problem → live demo → how you know it works
      (evals!) → costs and trade-offs → limitations and roadmap.
- [ ] Rules: demo live, don't read slides; when showing code, show the two files
      that matter (provider seam, judge rubric), not a file tour; say "here's where
      it fails" out loud — credibility comes from the limitations section.
- [ ] Watch your own recording once, list 3 fixes, record take two. Link the video
      in the README (unlisted is fine).

### 5. Publish and close the loop (~1h)

- [ ] Final secret scan, license, push everything public.
- [ ] Write a short post (LinkedIn/blog) on the single most transferable lesson —
      recommended topic: "calibrating an LLM judge against human labels", since
      almost nobody demonstrates it with real numbers.
- [ ] Update your portfolio/CV with the capstone: one line of problem, one line of
      measured results, link.

---

## Resources

- Mermaid diagrams in GitHub markdown: https://mermaid.js.org/
- Docker multi-stage builds: https://docs.docker.com/build/building/multi-stage/
- For a model of results-first technical writing, browse high-quality eval reports,
  e.g. Inspect AI's examples: https://inspect.aisi.org.uk/

---

## Deliverable

The finished, public capstone: dockerized one-command setup, dual-provider scorecards
with a portability writeup, decision-maker README with results table and Mermaid
diagram, and a 10-minute recorded walkthrough linked from the README.

## Self-check

1. Could a stranger run your system in 10 minutes from the README alone? (Test it:
   fresh clone, clean shell.)
2. If asked "how do you know it works?", is your answer one sentence with numbers?
3. What would you cut from the video to make it 7 minutes without losing the
   evals story?
