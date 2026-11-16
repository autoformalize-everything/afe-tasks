# Oracle: streams, decompressStream and decompress

Implement `decompressStream` and `decompress` in `Bzip2.Oracle` (with the parts in
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

## Ground rules

Read "The Lean formalization" in `projects/bzip2/README.md` first: the layout, the import rules
(the oracle uses no Mathlib and no spec), no `sorry`/`admit`/`axiom`, and the oracle's API in
`projects/bzip2/oracle/Bzip2/Oracle.lean`. Behaviour is the vendored bzip2's: the C sources in
`projects/bzip2/upstream` (commit f5dfc96: 1.0.8 plus later fixes), documented with citations in
`projects/bzip2/docs/surface-area.md`. Do not change `projects/bzip2/upstream` or `projects/bzip2/tools/`; build C
code out of tree. `projects/bzip2/tools/verify.sh` must pass when you finish.
