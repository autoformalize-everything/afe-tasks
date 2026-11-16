# Oracle: code lengths, tables and selectors

Add `Bzip2.Oracle.Compress.Huffman`: from the symbol sequence, choose the coding tables and
selectors as `sendMTFValues` in `projects/bzip2/upstream/compress.c` does: the number of tables from the number
of symbols (2 to 6), the initial partition by cumulative frequency, 4 refinement iterations,
code lengths by `BZ2_hbMakeCodeLengths` with maximum length 17 (`huffman.c`, including its
weight-scaling retry), and codes by `BZ2_hbAssignCodes`.

Follow the C arithmetic exactly (integer division, tie order in the heap): byte-identical output
depends on it.

## Done when

- Tests: lengths and selectors for symbol sequences taken from real blocks equal what the C code
  computes (a small C driver over `projects/bzip2/upstream` is the easiest way to get them).

## Ground rules

Read "The Lean formalization" in `projects/bzip2/README.md` first: the layout, the import rules
(the oracle uses no Mathlib and no spec), no `sorry`/`admit`/`axiom`, and the oracle's API in
`projects/bzip2/oracle/Bzip2/Oracle.lean`. Behaviour is the vendored bzip2's: the C sources in
`projects/bzip2/upstream` (commit f5dfc96: 1.0.8 plus later fixes), documented with citations in
`projects/bzip2/docs/surface-area.md`. Do not change `projects/bzip2/upstream` or `projects/bzip2/tools/`; build C
code out of tree. `projects/bzip2/tools/verify.sh` must pass when you finish.
