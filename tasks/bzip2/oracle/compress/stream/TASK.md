# Oracle: the stream writer and compress

Implement `compress` in `Bzip2.Oracle` (parts in `Bzip2.Oracle.Compress.Stream`): the `BZh`
header with the level, per block the magic, block CRC, randomised bit (always 0), `origPtr`,
symbol map, `nGroups`, `nSelectors`, MTF-coded unary selectors, delta-coded code lengths and the
coded symbols; then the end-of-stream magic, combined CRC and padding, as `compressStream` and
`BZ2_compressBlock` in `projects/bzip2/upstream/compress.c` write them. Reject levels outside 1..9 with
`Error.param`.

## Done when

- The vendored build's `bzip2 -d` decompresses the oracle's output for the upstream sample
  files and for the golden inputs, at levels 1 and 9; so does `bzip2-oracle -d`.
- The empty input gives the same 14 bytes as `bzip2 -c </dev/null`.

## Ground rules

Read "The Lean formalization" in `projects/bzip2/README.md` first: the layout, the import rules
(the oracle uses no Mathlib and no spec), no `sorry`/`admit`/`axiom`, and the oracle's API in
`projects/bzip2/oracle/Bzip2/Oracle.lean`. Behaviour is the vendored bzip2's: the C sources in
`projects/bzip2/upstream` (commit f5dfc96: 1.0.8 plus later fixes), documented with citations in
`projects/bzip2/docs/surface-area.md`. Do not change `projects/bzip2/upstream` or `projects/bzip2/tools/`; build C
code out of tree. `projects/bzip2/tools/verify.sh` must pass when you finish.
