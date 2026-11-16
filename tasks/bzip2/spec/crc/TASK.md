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

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/spec/Bzip2/Spec/Crc.lean`
- `projects/bzip2/spec/Bzip2/Spec/Crc/*`

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
