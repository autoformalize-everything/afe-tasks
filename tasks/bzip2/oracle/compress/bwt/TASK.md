# Oracle: forward BWT

Add `Bzip2.Oracle.Compress.Bwt`: the forward transform of a block and its `origPtr`. Start with
a correct algorithm that handles 900 000-byte blocks in reasonable time (a suffix-array style
prefix-doubling sort of the rotations is fine; avoid O(n² log n) comparisons on runs).

Matching the C library's `origPtr` on periodic blocks (equal rotations) is not required here; that is
`oracle/compress/byte-exact`. Say in the module doc what your tie-breaking is.

## Done when

- Tests: the transform of small blocks against a naive sort of the rotations; a 900 000-byte
  block of one repeated byte and one of random bytes each finish within a few seconds;
  inverting with `oracle/decompress/block-output` (once it exists) gives the block back.

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/oracle/Bzip2/Oracle/Compress/Bwt.lean`
- `projects/bzip2/oracle/Bzip2/Oracle/Compress/Bwt/*`
- `projects/bzip2/tests/Bzip2/Test/CompressBwt*.lean`
- `projects/bzip2/tests/vectors/compress-bwt/*`

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
