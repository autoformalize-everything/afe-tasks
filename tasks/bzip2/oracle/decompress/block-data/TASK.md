# Oracle: symbols to block bytes (MTF and RUNA/RUNB)

Add `Bzip2.Oracle.Decompress.Symbols`: decode a block's symbols, 50 per selector, until EOB,
undoing the zero-run code and move-to-front, as `getAndMoveToFrontDecode` in
`projects/bzip2/upstream/decompress.c` does: producing the BWT output (`tt`/`ll` contents and `unzftab` counts),
with its limits as errors: a run too long, a block over `100000 * level` bytes, running out of
selectors, EOB missing before the input ends.

Use `Array UInt32`/`ByteArray` with in-place updates; this is the hot loop.

## Done when

- Tests: blocks from real streams decode to the BWT output (check against a slow reference in
  the test), plus each limit case failing with the C error class.

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/oracle/Bzip2/Oracle/Decompress/Symbols.lean`
- `projects/bzip2/oracle/Bzip2/Oracle/Decompress/Symbols/*`
- `projects/bzip2/tests/Bzip2/Test/DecompressSymbols*.lean`
- `projects/bzip2/tests/vectors/decompress-symbols/*`

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
