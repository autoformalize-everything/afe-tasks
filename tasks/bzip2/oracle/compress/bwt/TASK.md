# Oracle: forward BWT

Add `Bzip2.Oracle.Compress.Bwt`: the forward transform of a block and its `origPtr`. Start with
a correct algorithm that handles 900 000-byte blocks in reasonable time (a suffix-array style
prefix-doubling sort of the rotations is fine; avoid O(n² log n) comparisons on runs).

Matching the C library's `origPtr` on periodic blocks (equal rotations) is not required here; that is
`oracle/compress/byte-exact`. Say in the module doc what your tie-breaking is.

## Done when

- Tests: the transform of small blocks against a naive sort of the rotations; a 900 000-byte
  block of one repeated byte and one of random bytes each finish within a few seconds;
  inverting with `oracle/decompress/block-output` (once it exists) gives the block back.

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
