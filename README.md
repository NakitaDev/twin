# twin

A digital-twin chatbot that answers questions as me — built deliberately **without an agent
framework**. No LangChain, no Agents SDK, no orchestration library: just raw HTTP requests to
the model API and a tool-calling loop written by hand.

That constraint is the point. Agent frameworks hide the loop — you hand them tools and they
handle the back-and-forth. Writing that loop yourself makes it obvious what's actually
happening: the model doesn't call your function, it *asks* you to, and you decide whether to
comply, what to send back, and when to stop.

## How it works

The whole thing is one conversation loop:

1. Send the conversation history plus a system prompt describing who I am and a list of tool
   definitions
2. The model either replies with text, or returns a request to call one of the tools
3. If it's a tool call, run the function, append the result to the history, and send it back
4. Repeat until the model returns a plain answer

**Tools available to the model:**

- `record_user_details` — logs a visitor's email (plus name/notes if given) when they want to get in touch
- `record_unknown_question` — logs any question the twin couldn't answer, instead of letting it make one up

Both tools push a notification via [Pushover](https://pushover.net) so I see the alert on my phone in real time.

**Persona context** comes from `context.py`, which builds the system prompt at import time by combining `summary.txt` (a short, hand-written bio) with the full text of `linkedin.pdf` (extracted via `pypdf`). That combined prompt tells the model who it's representing, what it can talk about, and when to use the tools above.

## Setup

This project uses [uv](https://docs.astral.sh/uv/):

```bash
uv sync
```

Create a `.env` file with:

- `OPENROUTER_API_KEY` — key for [OpenRouter](https://openrouter.ai), used to reach the model (`openai/gpt-5.4-mini` by default, set in `app.py`)
- `PUSHOVER_USER` — Pushover user key, for the notification tools
- `PUSHOVER_TOKEN` — Pushover application token, for the notification tools

You'll also need `linkedin.pdf` (an export of your own LinkedIn profile) and `summary.txt` (a short bio) in the project root — `context.py` reads both to build the persona.

## Run

```bash
uv run app.py
```

Opens a Gradio chat UI (default `http://127.0.0.1:7860`).

## Files

- `app.py` — Gradio UI and the hand-rolled chat/tool-calling loop
- `context.py` — builds the system prompt from `summary.txt` + `linkedin.pdf`
- `tools.py` — tool definitions, the Pushover-backed implementations, and the dispatcher that runs them
- `styles.py` — CSS/JS and example prompts for the Gradio UI
- `summary.txt` — short hand-written bio injected into the persona
- `linkedin.pdf` — LinkedIn profile export, parsed for persona context

## Notes

The tool-calling protocol here is written against the raw API shape rather than an SDK
abstraction, so switching model providers means changing the request format rather than
swapping a config value — a tradeoff I took on purpose to see where the coupling actually
lives.
