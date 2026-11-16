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
