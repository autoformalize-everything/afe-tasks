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

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/oracle/Bzip2/Oracle/Decompress/Block.lean`
- `projects/bzip2/oracle/Bzip2/Oracle/Decompress/Block/*`
- `projects/bzip2/tests/Bzip2/Test/DecompressBlock*.lean`
- `projects/bzip2/tests/vectors/decompress-block/*`

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
