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
- `projects/bzip2/tools/check-task.sh spec/rle1` passes: `projects/bzip2/checks/spec/rle1.lean` lists the declarations this task must provide, under the names other tasks import, and the examples that must hold.

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/spec/Bzip2/Spec/Rle1.lean`
- `projects/bzip2/spec/Bzip2/Spec/Rle1/*`

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
