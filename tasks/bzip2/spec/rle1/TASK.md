# Spec: the initial run-length encoding (RLE1)

Define, in `Bzip2.Spec.Rle1`, the first transform bzip2 applies to input bytes and the last it
undoes: a run of 4 to 255 equal bytes is written as 4 copies followed by a count byte (0..251)
of further copies.

- `decodeRle1 : List UInt8 → List UInt8` exactly as the vendored bzip2's decoder behaves on *any*
  byte string, including the cases a compressor never produces: count bytes above 251, and a
  block that ends right after 4 equal bytes with no count byte. Read
  `unRLE_obuf_to_output_FAST` and `_SMALL` in `bzlib.c` to settle them, and record each edge
  case in the module doc.
- `encodeRle1` as bzip2's compressor does it (`ADD_CHAR_TO_BLOCK`/`add_pair_to_block` in
  `bzlib.c`): runs are cut at 255.
- State the roundtrip `decodeRle1 (encodeRle1 x) = x`; prove it here if it is short, otherwise
  leave it to `bzip2/proofs/rle`.

Block boundaries (a run never spans two blocks) belong to `spec/format`, not here.

## Done when

- Definitions with module docs that list the decoder's edge cases and where each is decided in
  `bzlib.c`.
- Small `example`s, checked by `decide` or `rfl`, for runs of 3, 4, 5, 255 and 256 bytes and for
  a count byte above 251.

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
