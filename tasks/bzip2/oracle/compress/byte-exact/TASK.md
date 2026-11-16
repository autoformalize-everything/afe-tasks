# Oracle: byte-identical compression

Make `compress level x` byte-identical to the vendored bzip2's output for every input and level.

The known gap is the BWT's `origPtr` on blocks with equal rotations, where the C library's choice
depends on `mainSort` versus `fallbackSort` (blocks under 10 000 bytes, or when `mainSort` runs
out of work budget: `workFactor` 30) in `projects/bzip2/upstream/blocksort.c`. Find out exactly what decides it
and reproduce it without porting the whole sort if you can; port it if you must. Check the
other places a difference could hide: block boundaries, Huffman tie order, selector
refinement.

Then switch the byte-identity check in `projects/bzip2/fuzz/compress_diff.c` on, if that harness exists,
and run it.

## Done when

- Golden tests compare compressed output byte for byte at levels 1, 5 and 9 and pass, including
  periodic inputs (`abab…`, one repeated byte, a repeated 1000-byte pattern) under and over
  10 000 bytes.
- If `projects/bzip2/fuzz/compress_diff.c` exists: its byte-identity check is on and a 10-minute run
  finds nothing.

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/oracle/Bzip2/Oracle/Compress/*`
- `projects/bzip2/tests/Bzip2/Test/ByteExact*.lean`
- `projects/bzip2/tests/vectors/*`
- `projects/bzip2/fuzz/compress_diff.c`

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
