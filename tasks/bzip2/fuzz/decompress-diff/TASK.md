# Fuzz: differential decompression

Add `projects/bzip2/fuzz/decompress_diff.c`, a libFuzzer harness that decompresses its input with
libbz2 (`BZ2_bzBuffToBuffDecompress`, one stream, output capped at 16 MiB: treat
`BZ_OUTBUFF_FULL` as "skip") and with the oracle's `decompressStream`, and aborts unless both
give the same verdict (ok / magic / data / eof) and, on ok, the same bytes and the same number of
input bytes consumed.

- `projects/bzip2/fuzz/run.sh decompress SECONDS` builds if needed and runs it on
  `projects/bzip2/fuzz/corpus/decompress/`, seeded from `projects/bzip2/fuzz/corpus/seed/` (commit a small seed
  set: the upstream samples and a few hand-made streams).
- Every disagreement found while doing this is either an oracle bug (fix it here, add the input
  to the golden tests) or a spec question (file a followup for `bzip2/spec/*`).

## Done when

- `projects/bzip2/fuzz/run.sh decompress 120` finds nothing (the task's verify runs it); document in
  `projects/bzip2/fuzz/README.md` how to run longer campaigns and minimise a crash.

## Ground rules

Read "The Lean formalization" in `projects/bzip2/README.md` first: the layout, the import rules
(the oracle uses no Mathlib and no spec), no `sorry`/`admit`/`axiom`, and the oracle's API in
`projects/bzip2/oracle/Bzip2/Oracle.lean`. Behaviour is the vendored bzip2's: the C sources in
`projects/bzip2/upstream` (commit f5dfc96: 1.0.8 plus later fixes), documented with citations in
`projects/bzip2/docs/surface-area.md`. Do not change `projects/bzip2/upstream` or `projects/bzip2/tools/`; build C
code out of tree. `projects/bzip2/tools/verify.sh` must pass when you finish.
