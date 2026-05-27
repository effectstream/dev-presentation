# Live Demo Runbook — EVM + Midnight template

The 10-minute live segment. Goal: go from "nothing running" to a cross-chain dApp where an
NFT minted on EVM gets a **private property** attached via a Midnight ZK circuit, all unified
in one database — **without bridging**.

Template: `templates/evm-midnight-v2/`

---

## ⏱️ Before you go live (do this ~5–8 min before your slot)

The full stack takes a few minutes to boot and sync. **Pre-warm it** so that when you switch to
the terminal while live, blocks are already ticking and the frontend is live. Then while live you
*talk through* the commands (which are on the slide) while the already-running app does the demo.

This is the **documented quick-start flow** (see `docs/home/10-quickstart`):

```sh
git clone https://github.com/effectstream/effectstream.git
cd effectstream/templates/evm-midnight-v2

bun i          # install dependencies
bun run dev    # compiles contracts AND starts the full local stack
```

That's it — `bun run dev` compiles the contracts and brings up every service. No separate build
steps, no root install, no `link.sh`.

> `link.sh` is **only** for engine development (working on the `@effectstream/*` packages themselves
> from inside the monorepo). For launching a template — what we're demoing — ignore it and use the
> flow above.

✅ **Ready when you see:** the frontend at <http://localhost:10599> loads and the block height
top-right is incrementing. Confirm the API too: <http://localhost:9999/api/erc721> (returns `[]`
until you mint — that's expected). The Midnight step pauses on "Waiting to receive tokens… /
Wallet sync progress" while the local devnet funds the deploy wallet — that's normal, not a hang.

**Prereqs:** `bun`, plus the `compact` (Midnight) and `forge` (EVM) compilers on PATH, and Docker
running. **Free the ports** first if a previous run is still around:
```sh
NODE_ENV=development bunx orchestrator stop   # clean shutdown
# nuclear option if something is stuck:
lsof -ti tcp:10599,9999,3334,8545,9944,8088,6300 | xargs kill -9 2>/dev/null
```

---

## 🎬 While presenting — the narration + click path

You don't need to *run* the commands live (the stack is already warm). Walk the slide, then drive
the already-running app:

1. **Show the slide** with the commands — "this is the entire setup: clone, `bun i`, `bun run dev`."
2. **Switch to terminal** — point at the orchestrator output: "one command brought up six services —
   database, a local EVM chain, the full Midnight stack, our sync node, the batcher, and the frontend."
3. **Open <http://localhost:10599>** — "both chains' block heights are ticking live."
4. **Mint an ERC-721** on the EVM side (Wallet Demo component) — "public ownership on EVM."
5. **Watch it index** — "the sync node saw the Transfer event and it's in our database in about a second."
6. **Add a private property via Midnight** — "only the owner can run this ZK circuit; the result is
   published for the app to read." Batcher submits it; the record updates.
7. **Hit the raw API** <http://localhost:9999/api/erc721> — "real indexed state, one record, two chains."

### Ports
| Service | URL |
|---|---|
| Frontend (dApp) | <http://localhost:10599> |
| Node API | <http://localhost:9999> (e.g. `/api/erc721`) |
| Batcher | <http://localhost:3334> |
| Orchestrator API | <http://localhost:4747> (`status`, `logs`, `restart`) |
| Hardhat EVM | `:8545` · Midnight node `:9944` · indexer `:8088` · proof server `:6300` |

---

## 🛟 Fallback — if anything stalls while live

**Rule: do not debug live for more than ~30 seconds.** Switch to the screenshot slides in the deck
(Part 4, "If the live boot stalls") and narrate the exact same flow over the captured images, then
move on. The screenshots were captured from a real local boot, so the story is identical.

Common gotchas:
- **Frontend blank page** → Bun `ws` issue; `.env.dev` already sets `VITE_IS_BUN=true` (HTTP polling). Reload.
- **Port already in use** → run the `orchestrator stop` / `lsof … kill` block above, then `bun run dev`.
- **Sync stuck at block 0** → Hardhat isn't mining; restart just that process:
  `NODE_ENV=development bunx orchestrator restart <name>` (names via `orchestrator status`).
- **Midnight contract failed to deploy** → check `MIDNIGHT_STORAGE_PASSWORD` complexity in `start.dev.ts`.
- **Contract compile error on first `bun run dev`** → make sure the `compact` and `forge` compilers are on PATH.

## 🧹 After the session
```sh
NODE_ENV=development bunx orchestrator stop
```
