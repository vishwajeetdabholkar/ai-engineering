# Applied AI Engineering — an 8-Week, Build-First Learning Path

A structured, hands-on curriculum for becoming a production-grade **Applied AI Engineer**:
someone who can take an LLM use case from idea → prototype → **evaluated, observable,
production-ready system**, and explain every decision along the way.

This is not a "watch tutorials" plan. Every week ends with something you built,
and the final four weeks produce a complete capstone project — a multi-agent system
with a rigorous evaluation harness — that can stand on its own in a portfolio.

## Who this is for

Engineers who already write solid Python and want to go deep on the **application layer**
of AI: agents, retrieval, tool calling, guardrails, and above all **evaluation** — the
discipline that separates demos from production systems.

## Philosophy

1. **Evals before agents.** Most people build first and evaluate never. We learn
   evaluation in weeks 2–3 so that everything built afterward is measurable by design.
2. **One capstone, done deeply.** A single project with a golden dataset, calibrated
   LLM-as-judge, CI regression suite, and cost/latency analysis beats five shallow demos.
3. **Provider-agnostic by construction.** All code targets the OpenAI-compatible API
   standard, with the provider isolated in one config file. This path uses
   **[Together AI](https://www.together.ai/)** as the default provider — it's popular,
   OpenAI-compatible, and serves a wide range of strong open-weight models (great for
   comparing model families) — but everything here runs unchanged against OpenAI,
   Anthropic-compatible gateways, or a local server by changing one base URL.

## The 8 weeks at a glance

| Week | Theme | Deliverable |
|------|-------|-------------|
| [1](week_1/week_1_tasks.md) | Foundations: environment, provider-agnostic client, first agent | Working repo scaffold + first tool-calling agent |
| [2](week_2/week_2_tasks.md) | Evaluation fundamentals: datasets, graders, regression runs | `eval-playground` mini-project with CI |
| [3](week_3/week_3_tasks.md) | LLM-as-judge: rubrics, biases, calibration, trajectory evals | Calibrated judge with a human-agreement score |
| [4](week_4/week_4_tasks.md) | Agents: tools, handoffs, guardrails, deterministic testing | Multi-agent system rebuilt from first principles |
| [5](week_5/week_5_tasks.md) | Retrieval: embeddings, vector search, chunking, RAG evals | Evaluated RAG pipeline |
| [6](week_6/week_6_tasks.md) | Capstone I: architecture + core build | Working end-to-end capstone |
| [7](week_7/week_7_tasks.md) | Capstone II: eval harness, observability, cost engineering | Measured, monitored capstone |
| [8](week_8/week_8_tasks.md) | Production polish: docs, portability, presentation | Client-ready README + video walkthrough |

Budget: ~6–10 focused hours per week. Weeks 6–7 are the heaviest.

## Repo layout

```
ai-engineering/
├── README.md                  # this file
├── week_1/week_1_tasks.md     # tasks, resources, deliverables, self-check
├── week_2/week_2_tasks.md
├── ...
└── week_8/week_8_tasks.md
```

As you progress, each week's folder also holds the code you write for that week.

## Ground rules

- **A week without a commit is a failed week.** The artifacts are the learning.
- Read code > read blog posts. Every week links primary sources (repos, official docs)
  over commentary.
- Keep every artifact public-safe from day one: no secrets, no proprietary data,
  synthetic datasets only.

## License

Free- For/Clone and start learning
