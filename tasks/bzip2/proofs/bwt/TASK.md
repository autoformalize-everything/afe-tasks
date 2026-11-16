# Proof: the BWT, forward and inverse

In `Bzip2.Proofs.Bwt`, prove:

- the oracle's inverse (`cftab`/`tt` walk) computes the spec's inverse on valid inputs and fails
  exactly on invalid ones;
- the oracle's forward transform yields the spec's transform, with an `origPtr` that points at
  the block;
- hence inverse after forward is the identity on every block.

## Done when

- The theorems, with no `sorry`, imported by `Bzip2.Proofs`. The inverse direction first: it is
  what decompression needs.

## Ground rules

Read "The Lean formalization" in `projects/bzip2/README.md` first: the layout, the import rules
(the oracle uses no Mathlib and no spec), no `sorry`/`admit`/`axiom`, and the oracle's API in
`projects/bzip2/oracle/Bzip2/Oracle.lean`. Behaviour is the vendored bzip2's: the C sources in
`projects/bzip2/upstream` (commit f5dfc96: 1.0.8 plus later fixes), documented with citations in
`projects/bzip2/docs/surface-area.md`. Do not change `projects/bzip2/upstream` or `projects/bzip2/tools/`; build C
code out of tree. `projects/bzip2/tools/verify.sh` must pass when you finish.
