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

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/oracle/Bzip2/Oracle/Decompress/Header.lean`
- `projects/bzip2/oracle/Bzip2/Oracle/Decompress/Header/*`
- `projects/bzip2/tests/Bzip2/Test/DecompressHeader*.lean`
- `projects/bzip2/tests/vectors/decompress-header/*`

## Ground rules

Read "The Lean formalization" in `projects/bzip2/README.md` and all of `projects/bzip2/STYLE.md`
first; reviewers hold the change to exactly those rules and this task. Behaviour is the vendored
bzip2's: the C sources in `projects/bzip2/upstream` (commit f5dfc96: 1.0.8 plus later fixes), documented in
`projects/bzip2/docs/surface-area.md`. Cite them as `file.c:LINE` or `file.c:FROM-TO`, and read
the lines before you cite them. Build C code out of tree.

Change only the files listed under "Files" above: add your own modules, import what you need
directly, and leave everything else alone, in particular `projects/bzip2/upstream`, `projects/bzip2/tools/`,
`projects/bzip2/checks/` and the root modules. Unit tests are `#guard`s in the test module named
there.

`projects/bzip2/tools/verify.sh` must pass; it runs named stages (`packages sources build style axioms lint test`),
and after a failure you can rerun one: `projects/bzip2/tools/verify.sh style`. Quote its `verify: ...` lines in your
result's summary.
