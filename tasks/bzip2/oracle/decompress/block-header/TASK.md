# Oracle: block header and tables

Add `Bzip2.Oracle.Decompress.Header`: parse a block from just after its 48-bit magic up to the
first Huffman-coded symbol, as `BZ2_decompress` in `projects/bzip2/upstream/decompress.c` does: block CRC,
randomised bit, `origPtr`, the symbol map, `nGroups`, `nSelectors`, the MTF-coded selectors
(read past 18002 but ignored, as the C library does), and the delta-coded code lengths per group.

Return a structure the next stage uses (the used-byte list, alphabet size, selectors, one
decoding table per group from `oracle/decompress/huffman`) or the same error class, at the same
point, as the C code.

## Done when

- Tests on headers taken from real streams (levels 1 and 9, a block with all 256 byte values, a
  block with one byte value) and on corrupted fields: `nGroups` 1 and 7, `nSelectors` 0,
  a selector ≥ `nGroups`, a code length leaving 1..20, input ending inside the header.

## Ground rules

Read "The Lean formalization" in `projects/bzip2/README.md` first: the layout, the import rules
(the oracle uses no Mathlib and no spec), no `sorry`/`admit`/`axiom`, and the oracle's API in
`projects/bzip2/oracle/Bzip2/Oracle.lean`. Behaviour is the vendored bzip2's: the C sources in
`projects/bzip2/upstream` (commit f5dfc96: 1.0.8 plus later fixes), documented with citations in
`projects/bzip2/docs/surface-area.md`. Do not change `projects/bzip2/upstream` or `projects/bzip2/tools/`; build C
code out of tree. `projects/bzip2/tools/verify.sh` must pass when you finish.
