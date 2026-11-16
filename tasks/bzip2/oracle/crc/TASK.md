# Oracle: table-driven CRC

Add `Bzip2.Oracle.Crc`: the CRC bzip2 uses, computed with a 256-entry table like
`projects/bzip2/upstream/crctable.c` (generate the table in Lean rather than pasting it), over `ByteArray` slices,
plus the combined stream CRC (`rotl 1` then xor).

## Done when

- `crc32 "123456789" = 0xFC891918`, the empty input gives 0, and the table equals
  `BZ2_crc32Table` (a test compares a few entries with the C file).

## Ground rules

Read "The Lean formalization" in `projects/bzip2/README.md` first: the layout, the import rules
(the oracle uses no Mathlib and no spec), no `sorry`/`admit`/`axiom`, and the oracle's API in
`projects/bzip2/oracle/Bzip2/Oracle.lean`. Behaviour is the vendored bzip2's: the C sources in
`projects/bzip2/upstream` (commit f5dfc96: 1.0.8 plus later fixes), documented with citations in
`projects/bzip2/docs/surface-area.md`. Do not change `projects/bzip2/upstream` or `projects/bzip2/tools/`; build C
code out of tree. `projects/bzip2/tools/verify.sh` must pass when you finish.
