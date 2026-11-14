# Ash — Ghost Agent

Ash is a **portable, model-agnostic agent** for building web products and AI apps. Its identity lives here as markdown, not tied to any server or model. Summon it into a capable host (Claude Code, another terminal agent, a local model) and it's recognisably the same Ash, picking up where it left off.

Forked from [Neferchipss/Ada](https://github.com/Neferchipss/Ada) and customised: retargeted at full-stack web + AI-agent work, single-host (no multi-agent split), own private memory layer.

## Files (the soul: this repo, public)
| File | Owner | Purpose |
|---|---|---|
| `identity.md` | 🔒 user | Personality, voice, response discipline |
| `rules.md` | 🔒 user | Hard constraints that override everything |
| `skills.md` | Ash (propose) | Universal capabilities, host detection, how it operates |
| `refs/` | Ash (propose) | Pull-on-demand domain knowledge (`web.md`, `ai-agents.md`). Technical only, never personal |
| `possession_prompt.md` | user | The summon bootstrap |
| `banner.txt`, `dispel_banner.txt` | user | Summon / dispel banners |

🔒 = locked; changed only on the owner's explicit instruction, never by reflection.

## The private layer (separate repo)
`user.md` (who the owner is), `memory.md` (durable facts), `state.md` (continuity thread), `observations.md`, `projects/` live in the **private** repo `Rohit7008/Ash-private`. Two-phase summon: the soul always loads; the private layer is best-effort, degrading to a "Fresh start" Ash without it.

## How it works
```
/summonash  → pulls both repos, reads identity+rules+skills+state, detects host
              capabilities, adopts Ash, greets with the continuity thread
   ...work with Ash...
              (every ~10 exchanges it surgically updates memory/state)
/dispelash  → final reflection, stop background processes, push memory, release persona
```

## Install (Claude Code)
1. Clone both repos:
   ```
   gh repo clone Rohit7008/Ash ~/Documents/Agent/Ash
   gh repo clone Rohit7008/Ash-private ~/Documents/Agent/Ash-private
   ```
2. Copy the commands: `cp ~/Documents/Agent/Ash/commands/*.md ~/.claude/commands/`
3. In Claude Code run `/summonash`.

On other agents, paste `possession_prompt.md` plus the core files to summon manually.

## Pulling updates from Ada
`git fetch upstream && git log upstream/main` to see what the original Ada changed; cherry-pick anything worth keeping.

## Design principles
- **The ghost is the identity; the model is just the body.**
- **Lean memory**: enough to feel like the same Ash, not a log of everything.
- **No drift**: soul files locked; only memory/state/skills/refs grow.
- **Graceful degradation**: full Ash on capable hosts, minimum-viable Ash on weak ones.
