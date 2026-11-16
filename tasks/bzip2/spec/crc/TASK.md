# Spec: CRC-32 as bzip2 uses it

Define, in `projects/bzip2/spec/Bzip2/Spec/Crc.lean` (module `Bzip2.Spec.Crc`):

- the CRC-32 bzip2 uses: polynomial 0x04C11DB7, most significant bit first (not reflected),
  initial value 0xFFFFFFFF, final complement; as a bitwise definition over a list of bytes,
  not a table;
- the block CRC (the CRC of a block's original bytes, before any transform);
- the combined stream CRC: starting from 0, for each block `c := rotl1 c xor blockCrc`.

Use `BitVec 32` or `UInt32`, whichever makes later proofs easier; say which and why in the
module doc.

References: `projects/bzip2/upstream/crctable.c`, `BZ_UPDATE_CRC` in `bzlib_private.h`, `combinedCRC` in
`compress.c` and `decompress.c`.

## Done when

- The definitions exist in `Bzip2.Spec.Crc`.
- `example`s or `decide`/`rfl` checks show the CRC of `"123456789"` is 0xFC891918 and the CRC of
  the empty string is 0, and one combined-CRC example agrees with a real two-block stream.

## Ground rules

Read "The Lean formalization" in `projects/bzip2/README.md` first: the layout, the import rules
(the oracle uses no Mathlib and no spec), no `sorry`/`admit`/`axiom`, and the oracle's API in
`projects/bzip2/oracle/Bzip2/Oracle.lean`. Behaviour is the vendored bzip2's: the C sources in
`projects/bzip2/upstream` (commit f5dfc96: 1.0.8 plus later fixes), documented with citations in
`projects/bzip2/docs/surface-area.md`. Do not change `projects/bzip2/upstream` or `projects/bzip2/tools/`; build C
code out of tree.

Add your own files and change nothing other tasks share: Lake builds every module by glob, so
no file lists the parts. In particular do not edit the root modules `Bzip2/Spec.lean`,
`Bzip2/Oracle.lean` and `Bzip2/Proofs.lean` (verify rejects it), nor `lakefile.toml` or
`tests/Bzip2/Tests.lean` unless this task says so. Import the modules you need directly. Unit
tests go in a module of their own, `projects/bzip2/tests/Bzip2/Test/<Part>.lean`, as `#guard` and
`example`. If your branch is behind `origin/master`, merge it and keep your change minimal.
`projects/bzip2/tools/verify.sh` must pass when you finish.
