# Proof: compression produces a valid stream

In `Bzip2.Proofs.Compress`, prove `Oracle.compress l x = .ok z → Spec.ValidCompression l x z`,
and in particular `Spec.Decodes z x`; with `proofs/decompress` this gives
`Oracle.decompress (compress l x) = .ok x`.

As for `proofs/decompress`: the single-block case first, followups for what does not fit.

## Done when

- The theorem, with no `sorry`, in `Bzip2.Proofs.Compress`, re-exported from `Bzip2.Proofs`, or a
  partial result with followups.

## Ground rules

Read "The Lean formalization" in `projects/bzip2/README.md` first: the layout, the import rules
(the oracle uses no Mathlib and no spec), no `sorry`/`admit`/`axiom`, and the oracle's API in
`projects/bzip2/oracle/Bzip2/Oracle.lean`. Behaviour is the vendored bzip2's: the C sources in
`projects/bzip2/upstream` (commit f5dfc96: 1.0.8 plus later fixes), documented with citations in
`projects/bzip2/docs/surface-area.md`. Do not change `projects/bzip2/upstream` or `projects/bzip2/tools/`; build C
code out of tree. `projects/bzip2/tools/verify.sh` must pass when you finish.
