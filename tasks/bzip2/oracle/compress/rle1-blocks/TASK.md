# Oracle: input to blocks (RLE1, block CRCs)

Add `Bzip2.Oracle.Compress.Blocks`: split the input into blocks and apply RLE1 the way bzip2
the C library's compressor does (`ADD_CHAR_TO_BLOCK`, `add_pair_to_block`, `flush_RL` in `bzlib.c`):
a block holds at most `100000 * level - 19` bytes after RLE1, runs are cut at 255, the block
CRC is over the original bytes, and the symbol map (`inUse`) is collected per block.

The block boundaries must be the ones the C library picks, since byte-identical output depends on
them.

## Done when

- Tests: block boundaries at levels 1 and 9 for inputs around the limit, long runs across a
  boundary, the empty input (no blocks).

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
