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

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/oracle/Bzip2/Oracle/Compress/Huffman.lean`
- `projects/bzip2/oracle/Bzip2/Oracle/Compress/Huffman/*`
- `projects/bzip2/tests/Bzip2/Test/CompressHuffman*.lean`
- `projects/bzip2/tests/vectors/compress-huffman/*`

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
