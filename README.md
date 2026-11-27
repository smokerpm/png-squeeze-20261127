[![make.fun · png-squeeze-01-run-20261127](https://make.fun/badge/png-squeeze-01-run-20261127.svg)](https://make.fun/bounty/png-squeeze-01-run-20261127?utm_source=github&utm_medium=readme&utm_campaign=png-squeeze-01-run-20261127)
[![rank](https://make.fun/badge/png-squeeze-01-run-20261127/smokerpm/png-squeeze-20261127/rank.svg)](https://make.fun/bounty/png-squeeze-01-run-20261127/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=png-squeeze-01-run-20261127)
[![reward](https://make.fun/badge/png-squeeze-01-run-20261127/smokerpm/png-squeeze-20261127/reward.svg)](https://make.fun/bounty/png-squeeze-01-run-20261127?utm_source=github&utm_medium=readme&utm_campaign=png-squeeze-01-run-20261127)

# PNG Squeeze — smallest lossless PNGs

Entry for the [make.fun bounty **png-squeeze-01-run-20261127**](https://make.fun/bounty/png-squeeze-01-run-20261127): squeeze screenshots into the smallest PNG, 27 November 2026.

## What it does

`main.ts` (Node 22, standard library only) reads each `/input/<id>/image.png`
(1280×720 RGBA screenshot), decodes it, and writes the smallest lossless
re-encoding it can find to `/output/<id>/image.png`.

For every image it tries:

- **Forms** — palette (≤256 colours, ordered by count and by luminance),
  grey / grey+alpha, RGB / RGBA, and the single-transparent-value tRNS key,
  exactly the forms the scale compressor tries.
- **Filters** — every fixed filter 0–4, the minimum-sum heuristic, the
  fewest-distinct-byte-pairs heuristic, and per-row brute force (each filter
  scored by really deflating the row with the preceding 32 KiB as dictionary).
- **Deflate** — every candidate is screened at zlib level 6; the best two
  are re-compressed at level 9 with the default, filtered and Huffman-only
  strategies, and the smallest total (IDAT + PLTE/tRNS chunks) wins.

Only chunks the checker reads are written (IHDR, PLTE, tRNS, IDAT, IEND),
bit depth 8, no interlace. Output is fully deterministic: no clock reads,
no randomness — two runs write byte-identical files. On the kit practice
set it scores **703,048** (all 8 instances answered, `passed: 1`).

## How it runs

```sh
mkdir -p out && MAKEFUN_INPUT_DIR=practice/input MAKEFUN_OUTPUT_DIR=out node main.ts
```

`MAKEFUN_INPUT_DIR` / `MAKEFUN_OUTPUT_DIR` stand in for `/input` / `/output`
(the grader runs `node /bot/main.ts`). No network access, no files read
outside the input and output directories.

## Files

- `main.ts` — the whole encoder (decode, forms, filters, deflate search).
- `LICENSE` — MIT.
