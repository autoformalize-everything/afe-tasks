# Oracle: the stream writer and compress

Implement `compress` in `projects/bzip2/oracle/Bzip2/Oracle/Compress.lean`, which this task owns
(parts in `Bzip2.Oracle.Compress.Stream`): the `BZh`
header with the level, per block the magic, block CRC, randomised bit (always 0), `origPtr`,
symbol map, `nGroups`, `nSelectors`, MTF-coded unary selectors, delta-coded code lengths and the
coded symbols; then the end-of-stream magic, combined CRC and padding, as `compressStream` and
`BZ2_compressBlock` in `projects/bzip2/upstream/compress.c` write them. Reject levels outside 1..9 with
`Error.param`.

## Done when

- The vendored build's `bzip2 -d` decompresses the oracle's output for the upstream sample
  files and for the golden inputs, at levels 1 and 9; so does `bzip2-oracle -d`.
- The empty input gives the same 14 bytes as `bzip2 -c </dev/null`.
- `projects/bzip2/tools/check-task.sh oracle/compress/stream` passes: `projects/bzip2/checks/oracle/compress/stream.lean` lists the declarations this task must provide, under the names other tasks import, and the examples that must hold.

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/oracle/Bzip2/Oracle/Compress.lean`
- `projects/bzip2/oracle/Bzip2/Oracle/Compress/Stream.lean`
- `projects/bzip2/oracle/Bzip2/Oracle/Compress/Stream/*`
- `projects/bzip2/tests/Bzip2/Test/CompressStream*.lean`

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
