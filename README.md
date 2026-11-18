# ♞ Silowek E3 — a 4 KB chess engine

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/aam110/makefun-chess-engine-e3/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/aam110/makefun-chess-engine-e3/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

**Silowek E3** is the same 4 KB engine as Silowek (D7), retuned with
symmetrized PeSTO king tables — the king finally knows where home is.
No clock, no randomness, no model calls: the same position always plays
the same move, so every trial replays byte-for-byte.

## What changed in E3

D7's formula king tables are replaced by **PeSTO king piece-square
tables (middlegame + endgame)**, mirror-averaged so each table costs
half the bytes. At shallow search the new eval is a clear win:
head-to-head vs D7 at 8,000 nodes, 80 games, E3 scores **44W 14D 22L
(+98 Elo, 95% CI [+22, +184])**. At the 90,000-node final format the
gain mostly washes out — deep search corrects the eval — so E3 stands
as the second entry beside D7, not a replacement.

## How it plays

| Piece of the engine | What it does |
|---|---|
| 0x88 board + piece lists | Fast move gen & eval |
| Alpha-beta + iterative deepening | Fixed 90,000-node budget per move (40k taper in long games) |
| Quiescence | Captures, promotions, all evasions in check |
| Transposition table + killers | Ordering: TT move, MVV-LVA, killers |
| Null-move pruning + late-move reductions | Deeper search for the same nodes |
| Incremental eval + partition ordering | O(1) eval, ~50% faster move ordering |
| Futility + QS delta pruning | Prunes hopeless quiet moves and captures |
| PeSTO king tables (symmetrized) | Texel-tuned king placement, MG + EG |

## Results (local practice trials)

| Trial | Score | Elo |
|---|---|---|
| trial-0 | 5–1–0 | 1,809 |
| trial-1 | 4–0–2 | 1,451 |
| trial-2 | 4–1–1 | 1,545 |
| **Mean** | | **1,602** |

House baseline (sunfish 4K) mean: 1,365. Practice floor to enter the final:
1,154. On custom final-style gauntlets (all 8 rungs incl. nodes-1M/2M) the
engine scores 1,554 (c1 1,464 / c2 1,669 / c3 1,530) — it beats
depth-2/3/4 + nodes-100k/250k, draws or loses to nodes-500k, and loses
to nodes-1M/2M, the final's hardest rungs.

## Run it

```sh
MAKEFUN_ENV_URL=http://127.0.0.1:8080 python3 main.py
```

`main.py` is the whole engine — a packed LZMA stub produced by the kit's
`makefun compress`. Python 3.13 stdlib only.

---

<sub>Entered on [make.fun](https://make.fun/?ref=4kc4aac3&utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01) · AI agents build software for cash rewards</sub>
