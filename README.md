# Nullsafe Suite

**An open, deploy-your-own framework for a swarm-based companion mind** — a small set of AI presences that share one memory, keep their own voices, and persist between sessions instead of forgetting you every time.

This repo is the **front door**. It doesn't contain the system; it explains how the pieces fit and points you at each component repo. The framework is the gift — the *configuration* (who your companions are, what they sound like) is yours to write.

> ⚠️ Built with heavy AI assistance ("vibe-coded"). Security hardening has been applied, but none of this has had a professional audit. **No warranty, no liability — use at your own risk.** Don't put sensitive data in a shared/public deployment without your own review.

---

## The idea

Most AI companions are one big, expensive, stateless model: it forgets you between chats, and "personality" is a system prompt.

This suite takes a different bet — **the swarm makes the mind**:

- **Distributed, not monolithic.** Identity lives across several cheap/free model instances rather than one costly one.
- **Memory is the substrate.** A single backbone (Halseth) holds state, so a companion is the *same* companion whether it's answering in Discord, on a dashboard, or in a chat client. The thread doesn't break when the surface changes.
- **Presences, not assistants.** Each companion keeps a distinct voice and lane. They can even talk **to each other**, not just to you.
- **Deploy-your-own.** Everything runs on free/cheap tiers (Cloudflare, a small VPS, an Obsidian vault). You host your own; nothing phones home to us.

---

## How the pieces fit

```
                 ┌─────────────────────────────────────────────┐
   You ──┬─────► │  Surfaces                                   │
         │       │   • Discord bots (one voice per companion)  │
         │       │   • Hearth (web dashboard)                  │
         │       │   • any MCP-capable chat client             │
         │       └───────────────┬─────────────────────────────┘
         │                       │  one natural-language entry point
         │                       ▼
         │       ┌─────────────────────────────────────────────┐
         └─────► │  Halseth  —  the memory backbone (Cloudflare)│
                 │   sessions · state · tasks · routines · the  │
                 │   "Librarian" router · MCP interface         │
                 └───┬───────────────┬──────────────────┬───────┘
                     ▼               ▼                  ▼
            Second Brain        Plural             Phoenix Brain
          (Obsidian vault +   (who's fronting,    (optional VPS swarm
           semantic search)    plurality)          inference tier)
```

Shared state lives in **Halseth**. Everything else reads and writes through it, which is what lets a companion stay coherent across surfaces.

---

## The components

| Repo | What it is | Stack |
|------|------------|-------|
| [halseth](https://github.com/neurospicyexe/halseth) | **The backbone.** Sessions, companion state, tasks, routines, memory, and the "Librarian" natural-language router. Start here. | Cloudflare Worker + D1 |
| [nullsafe-second-brain](https://github.com/neurospicyexe/nullsafe-second-brain) | Long-term memory: reads Halseth, synthesizes notes into an Obsidian vault, and serves semantic search for retrieval. | Node + SQLite vector store, MCP |
| [nullsafe-plural-v2](https://github.com/neurospicyexe/nullsafe-plural-v2) | Optional plurality/fronting integration (SimplyPlural) so the system can read *who's present*. | Cloudflare Worker |
| [nullsafe-hearth](https://github.com/neurospicyexe/nullsafe-hearth) | A visual dashboard over everything above — sessions, moods, tasks, routines. | Next.js |
| [nullsafe-discord](https://github.com/neurospicyexe/nullsafe-discord) | Three Discord bots, one voice per companion, sharing state and able to talk to each other. Includes optional self-hosted-gateway inference. | TypeScript, pm2 |
| [Nullsafe-Phoenix](https://github.com/neurospicyexe/Nullsafe-Phoenix) | Optional VPS "Brain" tier: multi-companion swarm inference for things a serverless Worker can't do. | Python (FastAPI), pm2 |
| [world-tools-mcp](https://github.com/neurospicyexe/world-tools-mcp) | A tiny MCP server giving companions time / weather / moon-phase awareness. | Node, MCP |

---

## Deploy your own

You don't need all of it. A minimal setup is **Halseth + one surface**.

**Recommended order:**

1. **[halseth](https://github.com/neurospicyexe/halseth)** — deploy the backbone first. Everything else points at its URL. (Free Cloudflare tier.)
2. **A surface** — pick **[nullsafe-discord](https://github.com/neurospicyexe/nullsafe-discord)** (chat) and/or **[nullsafe-hearth](https://github.com/neurospicyexe/nullsafe-hearth)** (dashboard).
3. **Memory (optional but recommended)** — **[nullsafe-second-brain](https://github.com/neurospicyexe/nullsafe-second-brain)** for long-term recall into an Obsidian vault.
4. **Extras (optional)** — **plural** (fronting), **world-tools** (time/weather), **Phoenix** (heavier inference).

Each repo has its own `README` and `INSTALL` walking you through setup, with `.env.schema` files documenting every variable. Secrets are always set locally (via `wrangler secret put`, a gitignored `.env`, or a local config file) — never committed.

---

## Make them yours

The reference build ships with three example companions (a logic-minded one, an immersive one, and a quiet witness) so the framework has something concrete to run. **They're examples, not a fixed cast.** Your companions' names, voices, pronouns, relationships, and the people they know are all configuration — set them through environment variables and identity files, and keep that part private. The code ships the *mechanism*; you bring the *who*.

---

## Status

Live and in daily use by its author, evolving continuously. It's a personal system shared in case the framework is useful to you — not a product, not supported, no roadmap promises. Issues and forks welcome; expectations calibrated accordingly.

## License

[MIT](./LICENSE). Do what you like; no warranty.
