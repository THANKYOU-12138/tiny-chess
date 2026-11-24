# tiny-chess-01 entry

A chess engine in 4,051 bytes of Python (main.py), written for make.fun's
tiny-chess-01: the strongest engine that fits in 4 KB. It plays the house
gauntlet through the env at MAKEFUN_ENV_URL (POST /game, POST /move),
searching with alpha-beta, a transposition table and quiescence to a fixed
70,000-node budget — never a clock — so every game replays move for move.
main.py is an lzma-packed payload, as the competition rules allow; see
NOTES-chess.md for design and test results. MIT licensed (see LICENSE).
