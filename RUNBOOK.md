# Live Demo Runbook — EVM + Cardano template

The ~10-minute live segment. Goal: go from "nothing running" to a full **multi-chain local dev
environment** — mint an NFT on EVM, send ADA on Cardano, and watch both land in one unified event
feed. The hook: this replaces the **days-long Cardano indexer sync** with an instant localhost setup.

Template: `templates/evm-cardano/` (the "EVM-Cardano Explorer").

---

## ⏱️ Before you go live (do this ~5–8 min before your slot)

The full stack takes a few minutes to boot. **Pre-warm it** so that when you switch to the terminal
while live, the dashboard is already up and both chains' blocks are ticking. Then while live you
*talk through* the commands (which are on the slide) while the already-running app does the demo.

This is the **documented quick-start flow** (see `docs/home/quick-start` + the engine-integrations blog):

```sh
git clone https://github.com/effectstream/effectstream.git
cd effectstream/templates/evm-cardano

bun i          # install dependencies
bun run dev    # compiles contracts AND starts the full local stack
```

That's it — `bun run dev` compiles the EVM contracts and brings up every service. No separate build
steps, no root install, no `link.sh`.

> `link.sh` is **only** for engine development (working on the `@effectstream/*` packages themselves
> from inside the monorepo). For launching a template — what we're demoing — ignore it and use the
> flow above.

✅ **Ready when you see:** the **EVM-Cardano Explorer** at <http://localhost:10599> loads, and the
EVM (Hardhat) and Cardano (YACI) synced-block counters are both incrementing. Confirm the API too:
<http://localhost:9999/api/events> and <http://localhost:9999/api/stats>.

**Prereqs:** `bun`, the `forge` (EVM) compiler on PATH, and **Docker running** — the Cardano side
(YACI DevKit + Dolos) runs in containers. **Free the ports** first if a previous run is still around:
```sh
NODE_ENV=development bunx orchestrator stop   # clean shutdown
# nuclear option if something is stuck:
lsof -ti tcp:10599,9999,8545,10000,50051,3000 | xargs kill -9 2>/dev/null
```

---

## 🎬 While presenting — the narration + click path

You don't need to *run* the commands live (the stack is already warm). Walk the slide, then drive
the already-running Explorer dashboard:

1. **Show the slide** with the commands — "this is the entire setup: clone, `bun i`, `bun run dev`."
2. **Switch to terminal** — point at the orchestrator output: "one command brought up a database, a
   local EVM chain, a local Cardano devnet (YACI) with its Dolos indexer, deployed contracts, and the
   frontend — then the sync node. The Cardano dev env that normally takes *days* to sync is up in minutes."
3. **Open <http://localhost:10599>** — "the Explorer: EVM and Cardano block heights both ticking live."
4. **Mint an ERC-721** on the EVM side (one button, dev wallet — no extension needed).
5. **Watch it appear** in the unified **event feed** in ~a second — "the sync node indexed the EVM mint."
6. **Send ADA** on Cardano (use the in-app **Faucet** for funds, then Send) — it lands in the same feed.
7. **Hit the raw API** <http://localhost:9999/api/events> — "real indexed state, both chains, one feed."

### Ports
| Service | URL / Port |
|---|---|
| Frontend (Explorer dApp) | <http://localhost:10599> |
| Sync Node API | <http://localhost:9999> (`/api/events`, `/api/stats`) |
| Hardhat EVM | `:8545` |
| YACI DevKit (Cardano devnet) | `:10000` |
| Dolos gRPC (UTxORPC sync) | `:50051` |
| Dolos MiniBF (Blockfrost-compatible REST) | `:3000` |
| Orchestrator API | `:4747` (`status`, `logs`, `restart`) |

---

## 🛟 Fallback — if anything stalls while live

**Rule: do not debug live for more than ~30 seconds.** Switch to the deck's Part-4 fallback slides:
the **Explorer screenshot** (`assets/evm-cardano-explorer.png`) and the **recorded launch video**.
Narrate the same flow (mint → feed → send ADA → feed) over them, then move on.

Common gotchas:
- **Cardano side won't start** → Docker isn't running, or `:10000`/`:50051`/`:3000` are taken. Start Docker; free the ports.
- **Frontend blank page** → Bun `ws` issue; `.env.dev` already sets `VITE_IS_BUN=true` (HTTP polling). Reload.
- **Port already in use** → run the `orchestrator stop` / `lsof … kill` block above, then `bun run dev`.
- **Sync stuck at block 0** → restart just that process:
  `NODE_ENV=development bunx orchestrator restart <name>` (names via `orchestrator status`).
- **Contract compile error on first `bun run dev`** → make sure `forge` is on PATH.

## 🧹 After the session
```sh
NODE_ENV=development bunx orchestrator stop
```
