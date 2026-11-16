# Golden vectors and the lake test runner

Make `lake test` (`projects/bzip2/tests/Bzip2/Tests.lean`) run the oracle against golden vectors in
`projects/bzip2/tests/vectors/`, with expected results produced by the vendored bzip2, built out of tree from
`projects/bzip2/upstream`.

- Pick a simple on-disk format (for instance one directory per case: `input`, and either
  `expected` or `error` holding the class) and document it in `tests/vectors/README.md`; add a
  script that regenerates the expected files from the vendored bzip2.
- Decompression cases: the upstream samples, the empty stream, all 256 byte values, runs of 3,
  4, 5, 255, 256 and 100 000 equal bytes, a block exactly at the level-1 limit and one byte over,
  two concatenated streams, trailing garbage; failures: truncated in the header, in a block and
  in the footer, a flipped bit in a block CRC, a bad magic, level `0`.
- Compression cases: the same inputs at levels 1 and 9, checked by roundtrip through the oracle
  and through the vendored build's `bzip2 -d` (byte identity comes later, in
  `oracle/compress/byte-exact`).
- A case whose oracle function returns `Error.unimplemented` is reported as skipped, not failed,
  so the runner can land before the oracle does; every other mismatch fails `lake test`.

Keep the vectors small (a few MB in total).

## Done when

- `lake test` runs every case, reports passed/skipped/failed counts, and exits non-zero on any
  failure; with the current stubs everything is skipped.
- The regeneration script reproduces the committed expected files.

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/tests/Bzip2/Tests.lean`
- `projects/bzip2/tests/vectors/*`

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
