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

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/oracle/Bzip2/Oracle/Compress/Blocks.lean`
- `projects/bzip2/oracle/Bzip2/Oracle/Compress/Blocks/*`
- `projects/bzip2/tests/Bzip2/Test/CompressBlocks*.lean`
- `projects/bzip2/tests/vectors/compress-blocks/*`

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
