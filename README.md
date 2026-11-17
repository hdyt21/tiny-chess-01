# Tiny Chess Engine (make.fun tiny-chess-01)

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/hdyt21/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/hdyt21/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A compact chess engine in a single `main.py` (3,486 bytes, packed with the
kit's `makefun compress`), written for the
make.fun bounty "Build the strongest chess engine in 4 KB of Python".

This is the V6 engine: a PeSTO tapered evaluation on top of the v2a
search. Local practice grade (v9 kit, 3× 6-game trials): mean Elo 1,242
(V6), vs 1,219 for the v2a baseline. The engine also tracks repetition
history so it does not repeat a position it has already seen in the game.

## How it plays

- 0x88 board, negamax with alpha-beta pruning
- Quiescence search (captures, promotions, and all evasions when in check)
- Transposition table (Zobrist hashing), iterative deepening to a fixed
  node budget (150,000 nodes/move — deterministic, no clock)
- Move ordering: transposition-table move first, then MVV-LVA captures
  and promotions
- Evaluation: PeSTO tapered eval — separate middlegame and endgame
  piece-square tables, interpolated by game phase (phase counts
  non-pawn material: knight/bishop 1, rook 2, queen 4, max 24). Both
  scores and the phase are maintained incrementally (updated on
  make/unmake, so leaf evaluation is O(1)). The king centralises in
  the endgame, pawns advance more steeply, and a rook on the 7th rank
  earns a larger endgame bonus (25, up from 15).
- Deterministic: fixed node count, no randomness, no clock, no model calls

`main.py` is an LZMA-compressed payload with a tiny unpacker (the
compressed form is what keeps it under the 4,096-byte cap, as the
competition rules allow). The readable source it unpacks is a single-file
engine using only the Python standard library.

## Protocol

The engine talks to the environment at `MAKEFUN_ENV_URL`:
`POST /game {}` to fetch the position, `POST /move {"game": n,
"move": "e2e4"}` to play. It loops until every game is over.

## License

MIT — see `LICENSE`.
