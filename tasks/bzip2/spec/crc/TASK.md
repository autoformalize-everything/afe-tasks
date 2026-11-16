# Spec: CRC-32 as bzip2 uses it

Define, in `projects/bzip2/spec/Bzip2/Spec/Crc.lean` (module `Bzip2.Spec.Crc`):

- the CRC-32 bzip2 uses: polynomial 0x04C11DB7, most significant bit first (not reflected),
  initial value 0xFFFFFFFF, final complement; as a bitwise definition over a list of bytes,
  not a table;
- the block CRC (the CRC of a block's original bytes, before any transform);
- the combined stream CRC: starting from 0, for each block `c := rotl1 c xor blockCrc`.

Use `BitVec 32` or `UInt32`, whichever makes later proofs easier; say which and why in the
module doc. Import the module from `Bzip2/Spec.lean`.

References: `projects/bzip2/upstream/crctable.c`, `BZ_UPDATE_CRC` in `bzlib_private.h`, `combinedCRC` in
`compress.c` and `decompress.c`.

## Done when

- The definitions exist and are imported by `Bzip2.Spec`.
- `example`s or `decide`/`rfl` checks show the CRC of `"123456789"` is 0xFC891918 and the CRC of
  the empty string is 0, and one combined-CRC example agrees with a real two-block stream.

## Ground rules

Read "The Lean formalization" in `projects/bzip2/README.md` first: the layout, the import rules
(the oracle uses no Mathlib and no spec), no `sorry`/`admit`/`axiom`, and the oracle's API in
`projects/bzip2/oracle/Bzip2/Oracle.lean`. Behaviour is the vendored bzip2's: the C sources in
`projects/bzip2/upstream` (commit f5dfc96: 1.0.8 plus later fixes), documented with citations in
`projects/bzip2/docs/surface-area.md`. Do not change `projects/bzip2/upstream` or `projects/bzip2/tools/`; build C
code out of tree. `projects/bzip2/tools/verify.sh` must pass when you finish.
