# tiny-chess-01 notes

Caps/interface: main.py <= 4,096 bytes as committed (payload + unpacker
count); repo <= 8,192 bytes, no file over 4,096. Runs as python3 -P -s
/bot/main.py (Python 3.13, stdlib only), reads no repo file; JSON over HTTP
to MAKEFUN_ENV_URL: POST /game {}, POST /move {game, move:<uci>}, until
status done. 8 games/trial vs the house alpha-beta ladder (anchors
855..1712), Elo fit; practice floor 1,154; prizes also need beating the
sunfish-4k baseline (~1,365 practice mean). Determinism mandatory (final
trials replayed): fixed node budget, no clock, no unseeded randomness;
10 s/move, 1,800 s/trial.

Design: main.py is an lzma+base85 payload (explicitly allowed; the baseline
is packed the same way) unpacking to a ~7.6 KB engine written for this
entry: 0x88 board, full legal movegen, negamax alpha-beta, quiescence
(captures, promotions, evasions in check), transposition table, null-move
pruning, MVV-LVA ordering, iterative deepening to a fixed 70,000-node
budget, incremental material + piece-square eval (encoded tables) plus
bishop pair, zobrist hashing, in-search repetition/fifty-move draws. The
root move is cross-checked against the env's legal_moves, with a legal
fallback.

Tests (kit env + kit scorer, locally, on the committed file): perft exact
on 6 standard positions (startpos d4 = 197,281; kiwipete d3 = 97,862;
ep-pin d4 = 43,238). Practice trial-0: Elo 1364 (3W 1D 2L; beat
depth-2/3/4, drew nodes-250k), 517 s; a rerun gave a byte-identical state
export (determinism verified). Practice trial-1: Elo 1279 (3W 0D 3L; beat
depth-2, depth-4, nodes-250k), 505 s. Two-instance mean 1321 vs baseline
1,365: past the floor, near the baseline, no lock to beat it.

Sizes: main.py 4,051 bytes; repo total under the 8,192 cap.
