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
- `projects/bzip2/tools/check-task.sh spec/bwt` passes: `projects/bzip2/checks/spec/bwt.lean` lists the declarations this task must provide, under the names other tasks import, and the examples that must hold.

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/spec/Bzip2/Spec/Bwt.lean`
- `projects/bzip2/spec/Bzip2/Spec/Bwt/*`

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
