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

<!-- TODO: replace with your actual tools -->
**Tools available to the model:**

- `<tool_name>` — <what it does>
- `<tool_name>` — <what it does>

<!-- TODO: describe how the persona context is loaded -->
**Persona context** comes from `<file>`, which is <what it contains and how it's injected>.

## Setup

```bash
pip install -r requirements.txt
cp .env.example .env   # fill in your API keys
```

Required environment variables:

<!-- TODO: replace with your actual variables -->
- `<API_KEY>` — <what it's for>
- `<OTHER_KEY>` — <what it's for>

## Run

```bash
python app.py
```

<!-- TODO: confirm — Gradio? CLI? -->
Opens a `<Gradio UI / terminal chat>` at `<address>`.

## Files

<!-- TODO: replace with your actual files -->
- `app.py` — <what it does>
- `<file>.py` — <what it does>

## Notes

The tool-calling protocol here is written against the raw API shape rather than an SDK
abstraction, so switching model providers means changing the request format rather than
swapping a config value — a tradeoff I took on purpose to see where the coupling actually
lives.
