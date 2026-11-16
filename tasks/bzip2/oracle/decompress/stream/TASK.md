# Oracle: streams, decompressStream and decompress

Implement `decompressStream` and `decompress` in `projects/bzip2/oracle/Bzip2/Oracle/Decompress.lean`,
which this task owns (with the parts in
`Bzip2.Oracle.Decompress.Stream`): the `BZh` header and level, blocks until the end-of-stream
magic, the combined CRC, padding; then `decompress` over concatenated streams, handling what
follows the last stream as `uncompressStream` in `projects/bzip2/upstream/bzip2.c` does.

The error class and the order of checks must match the C library (`BZ2_bzDecompress` via
`BZ2_bzBuffToBuffDecompress` for one stream): the diff fuzzer compares them. Write down in the
module doc every place where `decompress` differs from a single `BZ2_bzBuffToBuffDecompress`
call.

## Done when

- `bzip2-oracle -d` decompresses the upstream test files (`sample1.bz2`, `sample2.bz2`,
  `sample3.bz2` in `projects/bzip2/upstream`) to their `.ref` files; the golden tests, if they
  exist by then, pass for decompression.
- Truncated streams give `eof`, a bad header `magic`, a bad stream CRC `data`.
- `projects/bzip2/tools/check-task.sh oracle/decompress/stream` passes: `projects/bzip2/checks/oracle/decompress/stream.lean` lists the declarations this task must provide, under the names other tasks import, and the examples that must hold.

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/oracle/Bzip2/Oracle/Decompress.lean`
- `projects/bzip2/oracle/Bzip2/Oracle/Decompress/Stream.lean`
- `projects/bzip2/oracle/Bzip2/Oracle/Decompress/Stream/*`
- `projects/bzip2/tests/Bzip2/Test/DecompressStream*.lean`

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
