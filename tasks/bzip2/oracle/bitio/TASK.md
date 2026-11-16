# Oracle: bit reader and writer

Add `Bzip2.Oracle.BitIO`: reading and writing bits over `ByteArray`, most significant bit
first, as bzip2 does (`GET_BITS` in `decompress.c`, `bsW` in `compress.c`).

- A reader with `readBits (n : Nat) (≤ 32)`, `readBit`, the position, and end-of-input reported
  as `Error.eof`.
- A writer with `writeBits`, `writeByte`, and `finish` (pad with zero bits to a byte).
- Fast: no per-bit allocation; it will run over 100 MB inputs.

Unit-test it in `projects/bzip2/tests/Bzip2/Test/BitIO.lean`, and keep it free of
anything bzip2-specific beyond the bit order.

## Done when

- The module exists and its tests cover reads that cross byte
  boundaries, 32-bit reads, reading past the end, and write-then-read roundtrips.
- `projects/bzip2/tools/check-task.sh oracle/bitio` passes: `projects/bzip2/checks/oracle/bitio.lean` lists the declarations this task must provide, under the names other tasks import, and the examples that must hold.

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/oracle/Bzip2/Oracle/BitIO.lean`
- `projects/bzip2/oracle/Bzip2/Oracle/BitIO/*`
- `projects/bzip2/tests/Bzip2/Test/BitIO*.lean`

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
