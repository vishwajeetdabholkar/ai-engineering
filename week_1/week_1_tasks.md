# Week 1 — Foundations: Environment, Provider-Agnostic Client, First Agent

**Theme:** set up a clean, professional workspace and get an LLM doing real work
(tool calls, structured output) through a provider layer you fully control.

**Why this matters:** every serious AI application isolates the model provider behind
one seam. Building that seam on day one means everything you create for the next
8 weeks is portable across OpenAI, Together AI, or a local server by changing one URL.

We use **Together AI** as the default provider throughout this path — it's popular,
OpenAI-compatible, and serves many strong open-weight model families, which makes
cross-model comparison (something you'll do constantly in evals) easy and cheap.
Any OpenAI-compatible provider works identically.

---

## Learning goals

By the end of this week you can:

- Explain the OpenAI-compatible API standard (chat completions, tool calls, streaming).
- Point any OpenAI SDK client at a different provider with `base_url`.
- Get reliable **structured output** (JSON schema / Pydantic) from a model.
- Write and invoke a **function tool** and understand the tool-calling loop
  (model proposes call → you execute → feed result back → model continues).

---

## Tasks

### 1. Scaffold the project (~1h)

- [ ] Create a repo with `pyproject.toml` or `requirements.txt`, `.gitignore`,
      `.env.example` (never commit real keys), and a `src/` layout.
- [ ] `pip install openai openai-agents pydantic python-dotenv`
- [ ] Write the provider seam — the single most important file of the whole path:

```python
# src/models.py — the only place a provider is named
import os
from openai import AsyncOpenAI

client = AsyncOpenAI(
    base_url=os.getenv("LLM_BASE_URL", "https://api.together.xyz/v1"),
    api_key=os.environ["LLM_API_KEY"],
)

CHAT_MODEL = os.getenv("CHAT_MODEL", "moonshotai/Kimi-K2-Instruct")   # any tool-calling model
JUDGE_MODEL = os.getenv("JUDGE_MODEL", "zai-org/GLM-4.5-Air")         # a DIFFERENT family (see week 3)
```

> Pick any two strong tool-calling models from your provider's catalog; the point is
> the agent model and the judge model come from **different families**.

### 2. Hello, chat completions (~1h)

- [ ] Write `01_chat_basics.py`: non-streaming call, streaming call, system prompt,
      temperature comparison. Print token usage for each call.
- [ ] Read the request/response shapes carefully — you will debug these for years:
      https://platform.openai.com/docs/api-reference/chat

### 3. Structured output (~1.5h)

- [ ] Define a Pydantic model (e.g. `TicketClassification` with `category`,
      `urgency`, `summary`) and get the model to fill it via JSON-schema response
      format. Handle the failure case (invalid JSON) explicitly.
- [ ] Note which of your provider's models support native JSON schema vs. need
      prompt-based JSON — write your findings in a `NOTES.md`.

### 4. The tool-calling loop, by hand (~2h)

- [ ] Write `02_tool_loop.py` implementing the loop **manually** (no framework):
      define two tools (`get_weather(city)` stub, `calculate(expression)`),
      pass their JSON schemas, execute the model's tool calls, feed results back,
      loop until a final answer. This demystifies everything agents do.

### 5. Same thing with the Agents SDK (~1.5h)

- [ ] Install and read the quickstart: https://openai.github.io/openai-agents-python/
- [ ] Rebuild task 4 with the SDK using a custom client:

```python
from agents import Agent, Runner, OpenAIChatCompletionsModel, function_tool, set_tracing_disabled
from src.models import client, CHAT_MODEL

set_tracing_disabled(True)  # tracing uploads target OpenAI's platform; disable when off-platform

@function_tool
def calculate(expression: str) -> str:
    """Evaluate a basic arithmetic expression."""
    return str(eval(expression, {"__builtins__": {}}))  # toy only — never in prod

agent = Agent(
    name="Calculator",
    instructions="Use tools for any arithmetic. Be concise.",
    model=OpenAIChatCompletionsModel(model=CHAT_MODEL, openai_client=client),
    tools=[calculate],
)
print(Runner.run_sync(agent, "What is 1234 * 5678 + 91?").final_output)
```

- [ ] Compare your manual loop with what the SDK does for you. Write 5 bullet points
      in `NOTES.md`: what the framework adds, what it hides.

---

## Resources

- OpenAI API reference (the lingua franca): https://platform.openai.com/docs/api-reference
- Together AI docs (OpenAI compatibility): https://docs.together.ai/docs/openai-api-compatibility
- Agents SDK repo + examples: https://github.com/openai/openai-agents-python
- OpenAI Cookbook (bookmark; used all 8 weeks): https://github.com/openai/openai-cookbook

---

## Deliverable

A repo containing `src/models.py`, the four scripts above, and `NOTES.md` with your
structured-output findings and framework-vs-manual comparison.

## Self-check (answer without looking)

1. What exactly travels over the wire when a model "calls a tool"? Who executes it?
2. Why is `base_url` isolation more robust than if/else per provider?
3. What are two ways structured output can fail, and how do you defend each?
