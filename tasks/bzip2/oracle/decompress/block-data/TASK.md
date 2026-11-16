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

## Ground rules

Read "The Lean formalization" in `projects/bzip2/README.md` first: the layout, the import rules
(the oracle uses no Mathlib and no spec), no `sorry`/`admit`/`axiom`, and the oracle's API in
`projects/bzip2/oracle/Bzip2/Oracle.lean`. Behaviour is the vendored bzip2's: the C sources in
`projects/bzip2/upstream` (commit f5dfc96: 1.0.8 plus later fixes), documented with citations in
`projects/bzip2/docs/surface-area.md`. Do not change `projects/bzip2/upstream` or `projects/bzip2/tools/`; build C
code out of tree.

Add your own files and change nothing other tasks share: Lake builds every module by glob, so
no file lists the parts. In particular do not edit the root modules `Bzip2/Spec.lean`,
`Bzip2/Oracle.lean` and `Bzip2/Proofs.lean` (verify rejects it), nor `lakefile.toml` or
`tests/Bzip2/Tests.lean` unless this task says so. Import the modules you need directly. Unit
tests go in a module of their own, `projects/bzip2/tests/Bzip2/Test/<Part>.lean`, as `#guard` and
`example`. If your branch is behind `origin/master`, merge it and keep your change minimal.
`projects/bzip2/tools/verify.sh` must pass when you finish.
