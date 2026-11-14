# Tiny Chess Engine (make.fun tiny-chess-01)

A compact chess engine in a single `main.py` (4,092 bytes), written for the
make.fun bounty "Build the strongest chess engine in 4 KB of Python".

## How it plays

- 0x88 board, negamax with alpha-beta pruning
- Quiescence search (captures, promotions, and all evasions when in check)
- Transposition table (Zobrist hashing), iterative deepening to a fixed
  node budget (150,000 nodes/move — deterministic, no clock)
- Move ordering: transposition-table move first, then MVV-LVA captures
  and promotions
- Evaluation: material + piece-square tables, maintained incrementally
  (the score is updated on make/unmake, so leaf evaluation is O(1))
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
