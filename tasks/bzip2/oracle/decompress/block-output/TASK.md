# Oracle: inverse BWT, derandomising, RLE1, block CRC

Add `Bzip2.Oracle.Decompress.Block`: from the BWT output and `origPtr`, produce the block's
bytes: check `origPtr` against the block length, build `cftab` and the `tt` links, walk them
(`BZ_GET_FAST`), apply randomisation when the block's flag is set (`BZ_RAND_MASK` with the
`BZ2_rNums` table from `randtable.c`), undo RLE1 as `unRLE_obuf_to_output_FAST` in `bzlib.c` does
(count bytes over 251, a run cut off by the block end), and compare the block CRC.

Then `decodeBlock`: header, symbols and output together, for one block.

## Done when

- Tests: real blocks decode to the right bytes; a randomised block (bzip2 0.9.0 era; make one by
  hand or find one in the upstream test files) decodes; a wrong block CRC gives `Error.data`;
  `origPtr ≥ nblock` gives `Error.data`.

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
