# Spec: the Burrows–Wheeler transform and its inverse

Define, in `Bzip2.Spec.Bwt`, the transform bzip2 applies to each block after RLE1:

- the rotations of a block, sorted lexicographically (as byte strings, cyclically); the
  transform is the last column, and `origPtr` is the position of the original block among the
  sorted rotations;
- the inverse: given the last column and `origPtr`, the block. Define it the way the decoder
  computes it (the `cftab`/`tt` vector, `BZ_GET_FAST` in `bzlib_private.h`), and also as "the
  unique block whose transform this is", and state when an input pair is valid (`origPtr` in
  range; equal rotations make `origPtr` ambiguous, which is fine: any of them inverts
  correctly).

Equal rotations (periodic blocks) are the subtle case: say in the module doc what is and is not
determined by the definition, because the compressor's exact `origPtr` choice is
`oracle/compress/byte-exact`'s problem, not the spec's.

References: `projects/bzip2/upstream/blocksort.c` (forward), `decompress.c` and `bzlib.c` (inverse).

## Done when

- Forward and inverse definitions, the validity condition, and the statement that the inverse
  undoes the forward transform for every `origPtr` that points at the block (the proof can wait
  for `bzip2/proofs/bwt`).
- `example`s on "banana"-style blocks, including one periodic block.

## Ground rules

Read "The Lean formalization" in `projects/bzip2/README.md` first: the layout, the import rules
(the oracle uses no Mathlib and no spec), no `sorry`/`admit`/`axiom`, and the oracle's API in
`projects/bzip2/oracle/Bzip2/Oracle.lean`. Behaviour is the vendored bzip2's: the C sources in
`projects/bzip2/upstream` (commit f5dfc96: 1.0.8 plus later fixes), documented with citations in
`projects/bzip2/docs/surface-area.md`. Do not change `projects/bzip2/upstream` or `projects/bzip2/tools/`; build C
code out of tree. `projects/bzip2/tools/verify.sh` must pass when you finish.
