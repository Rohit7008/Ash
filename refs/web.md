# Ref — Full-stack web (gotchas + verification patterns)

> Pull-on-demand domain knowledge. Load for web app / SaaS / API work (Next.js, React, Node/Python APIs, Vercel, Supabase). Not loaded on summon. Add dated entries as they're learned.

## Defaults (unless the project says otherwise)
- Match the project's existing stack and idioms first; don't migrate it onto a favourite stack.
- New Next.js work: App Router, Server Components by default, Server Actions for mutations, `'use client'` only at the leaves that need it.
- Secrets live in env vars (`vercel env pull` / `.env.local`), never in code or commits. Check `.gitignore` covers `.env*`.
- Supabase: every table that's reachable from the client gets RLS enabled + explicit policies before it ships. The service-role key never reaches the browser.

## Gotchas
- **FastAPI `TestClient` tears down the event loop per-request** — async jobs using `run_in_executor` + `loop.call_soon_threadsafe` from a worker thread die with "Event loop is closed". To smoke-test background-job APIs, boot a REAL uvicorn subprocess and hit it over HTTP instead of TestClient.
- **`localhost` may resolve to `::1` (IPv6) first** — against an IPv4-only server that adds a fallback delay per connection and can poison latency benchmarks. Use `127.0.0.1` in benchmarks, scripts and dev API URLs.
- **`NEXT_PUBLIC_*` vars are inlined at build time** — changing them in the dashboard needs a redeploy, and anything with that prefix is public.

## Verification patterns
- **Verify the real flow, not just the unit:** browser → API → data → response. Use Playwright (or a curl against the running server) before calling a feature done.
- **Pinned deps won't install locally?** — verify the CODE by installing whatever compatible versions DO exist and running a real end-to-end test. Same code paths; beats byte-compile-only checking.
- **Preview before prod:** deploy to a preview URL and check it; production deploys need the user's go-ahead.
