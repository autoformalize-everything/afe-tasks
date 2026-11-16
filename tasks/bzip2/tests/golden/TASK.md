# Golden vectors and the lake test runner

Make `lake test` (`projects/bzip2/tests/Bzip2/Tests.lean`) run the oracle against golden vectors in
`projects/bzip2/tests/vectors/`, with expected results produced by the vendored bzip2, built out of tree from
`projects/bzip2/upstream`.

- Pick a simple on-disk format (for instance one directory per case: `input`, and either
  `expected` or `error` holding the class) and document it in `tests/vectors/README.md`; add a
  script that regenerates the expected files from the vendored bzip2.
- Decompression cases: the upstream samples, the empty stream, all 256 byte values, runs of 3,
  4, 5, 255, 256 and 100 000 equal bytes, a block exactly at the level-1 limit and one byte over,
  two concatenated streams, trailing garbage; failures: truncated in the header, in a block and
  in the footer, a flipped bit in a block CRC, a bad magic, level `0`.
- Compression cases: the same inputs at levels 1 and 9, checked by roundtrip through the oracle
  and through the vendored build's `bzip2 -d` (byte identity comes later, in
  `oracle/compress/byte-exact`).
- A case whose oracle function returns `Error.unimplemented` is reported as skipped, not failed,
  so the runner can land before the oracle does; every other mismatch fails `lake test`.

Keep the vectors small (a few MB in total).

## Done when

- `lake test` runs every case, reports passed/skipped/failed counts, and exits non-zero on any
  failure; with the current stubs everything is skipped.
- The regeneration script reproduces the committed expected files.

## Ground rules

Read "The Lean formalization" in `projects/bzip2/README.md` first: the layout, the import rules
(the oracle uses no Mathlib and no spec), no `sorry`/`admit`/`axiom`, and the oracle's API in
`projects/bzip2/oracle/Bzip2/Oracle.lean`. Behaviour is the vendored bzip2's: the C sources in
`projects/bzip2/upstream` (commit f5dfc96: 1.0.8 plus later fixes), documented with citations in
`projects/bzip2/docs/surface-area.md`. Do not change `projects/bzip2/upstream` or `projects/bzip2/tools/`; build C
code out of tree. `projects/bzip2/tools/verify.sh` must pass when you finish.
