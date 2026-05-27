# EffectStream Intro Presentation

A reusable ~25-minute talk (+ Q&A) introducing EffectStream and live-launching an EVM + Midnight template.

**▶ Live deck: https://effectstream.github.io/dev-presentation/**

## Files
- **`index.html`** — the slide deck (reveal.js, loads from CDN). Open it in any browser.
- **`RUNBOOK.md`** — the live-demo script: exact commands, pre-warm steps, demo click-path, and a fallback plan if the live boot stalls.
- **`assets/`** — diagram + demo screenshots used in the deck.

## Presenting
Open `index.html` in a browser, then:

| Key | Action |
|-----|--------|
| `→` / `Space` | Next slide |
| `←` | Previous |
| `S` | **Speaker-notes view** (notes + timer + next-slide preview) |
| `F` | Fullscreen |
| `Esc` / `O` | Slide overview |

To open it directly:
```sh
open presentation/index.html          # macOS
# or serve it (better for the Notes window): 
bunx serve presentation               # then visit the printed URL
```

> Tip: use the **`S`** speaker view on your laptop and share/fullscreen the main window for the audience.

## Structure (~25 min)
1. **Why multi-chain dApps** — context & the problem (3 min)
2. **How EffectStream works** — sync → state machine → indexed state; batcher; non-custodial (4 min)
3. **Demos in the wild** — Safe Solver, Tarochi, EVM-Cardano Explorer (3 min)
4. **Live: launch the EVM + Midnight template** (10 min) — see `RUNBOOK.md`
5. **Tools, links, try it** (3 min)

Speaker notes for every slide are embedded in `index.html` — press `S`.
