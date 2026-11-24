# tiny-chess-01 notes

Caps/interface: main.py <= 4,096 B as committed; repo <= 8,192 B. Runs as
python3 -P -s /bot/main.py (Python 3.13, stdlib only); JSON over HTTP to
MAKEFUN_ENV_URL: POST /game {}, POST /move {game, move:<uci>} until status
done. 8 games/trial vs the house ladder (anchors 855..1712); practice floor
1,154; paid places also need beating the sunfish-4k baseline (~1,365).
Determinism mandatory: fixed node budget, no clock, no unseeded randomness;
10 s/move, 1,800 s/trial.

Design: main.py is an lzma+base85 payload (explicitly allowed; the baseline
is packed the same way) unpacking to a ~7.3 KB engine written for this entry:
0x88 board, full legal movegen, negamax alpha-beta, quiescence, transposition
table, null-move pruning, MVV-LVA + killer ordering, principal-variation
search, late-move reductions, incremental material + piece-square eval plus
bishop pair, zobrist hashing, in-search repetition/fifty-move draws. The root
move is cross-checked against the env's legal_moves, with a legal fallback.

v1 tests: perft exact on 6 positions (startpos d4 = 197,281; kiwipete d3 =
97,862; ep-pin d4 = 43,238); practice Elo 1364 / 1279 (mean 1321); rerun state
export byte-identical.

v2 (this entry): node budget 70k -> 100k, PVS, LMR, killers, merged
quiescence, tighter PST storage; fixes a v1 bug where a black en-passant
capture added the pawn's value instead of subtracting it. Practice trial-0
Elo 1809 (5W 1D 0L), trial-1 Elo 1191 (2W 1D 3L), mean 1500. Verified with the
kit's grade-local on Python 3.13: two full trial-0 runs plus the earlier
build run gave byte-identical state exports (sha256 ca66c295...), and each
run's replay check matched byte for byte.

Sizes: main.py 4,080 B; repo total under the 8,192 cap.
