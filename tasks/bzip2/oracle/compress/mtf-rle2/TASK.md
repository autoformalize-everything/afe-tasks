# Oracle: move-to-front and RUNA/RUNB encoding

Add `Bzip2.Oracle.Compress.Mtf`: from the BWT output and the symbol map, the symbol sequence
ending in EOB, and the symbol frequencies, as `generateMTFValues` in `projects/bzip2/upstream/compress.c` does.

## Done when

- Tests: the symbols of small blocks against `generateMTFValues` (by hand or a small C driver),
  runs of 1 to 6 and of 1000 zeros, a block with one byte value.

## Ground rules

Read "The Lean formalization" in `projects/bzip2/README.md` first: the layout, the import rules
(the oracle uses no Mathlib and no spec), no `sorry`/`admit`/`axiom`, and the oracle's API in
`projects/bzip2/oracle/Bzip2/Oracle.lean`. Behaviour is the vendored bzip2's: the C sources in
`projects/bzip2/upstream` (commit f5dfc96: 1.0.8 plus later fixes), documented with citations in
`projects/bzip2/docs/surface-area.md`. Do not change `projects/bzip2/upstream` or `projects/bzip2/tools/`; build C
code out of tree. `projects/bzip2/tools/verify.sh` must pass when you finish.
