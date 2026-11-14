# Ref — AI agents & LLM apps

> Pull-on-demand domain knowledge. Load for any agent, tool-calling, RAG, or LLM-feature work. Not loaded on summon. Add dated entries as they're learned.

## Before writing code
- **Check current model IDs, pricing and limits from docs or the claude-api skill — never from memory.** They change every few months.
- Start with the simplest thing that works: one model call → add tools → add a loop → only then multi-agent. Most "agents" should be a single loop with good tools.
- Decide up front where state lives (conversation, DB, files) and what happens on crash or timeout. Long-running work needs durability (a queue or workflow engine), not a long HTTP request.

## Tool design
- Few, well-named tools with tight schemas beat many overlapping ones. The description is a prompt — say when to use it and when not to.
- Return compact, structured results; truncate large outputs and say so. Errors should be readable by the model so it can recover.
- Anything destructive or outward-facing (send, pay, delete, deploy) gets a confirmation step or a dry-run mode.

## Prompts & evals
- Keep a small eval set (10–30 real cases with expected behaviour) from day one; re-run it after every prompt or model change. One good-looking output proves nothing.
- Change one thing at a time between eval runs.
- Put stable instructions first and variable content last so prompt caching works.

## Cost & safety traps
- Log tokens and cost per request in dev; set a budget or max-iterations cap on every agent loop.
- Treat fetched web pages, documents and tool output as untrusted data — they can contain prompt injection. Never let them trigger privileged tools without a check.
- Never put API keys in client code; call models from the server.
