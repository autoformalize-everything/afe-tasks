# Proof: decompression is sound and complete

In `Bzip2.Proofs.Decompress`, assemble the per-part results into the end results for
decompression:

- soundness: `Oracle.decompress x = .ok y → Spec.Decodes x y`;
- completeness: `Spec.Decodes x y → Oracle.decompress x = .ok y`;
- failures: when the oracle fails, the spec's first failure class is the same.

This is the largest proof in the project. Do the single-block, single-stream case first; if the
whole does not fit in one attempt, commit what is complete (no `sorry`) and file the rest as
followups with precise statements.

## Done when

- The three theorems, with no `sorry`, in `Bzip2.Proofs.Decompress`, re-exported from
  `Bzip2.Proofs`, or a partial result with followups as described.

## Ground rules

Read "The Lean formalization" in `projects/bzip2/README.md` first: the layout, the import rules
(the oracle uses no Mathlib and no spec), no `sorry`/`admit`/`axiom`, and the oracle's API in
`projects/bzip2/oracle/Bzip2/Oracle.lean`. Behaviour is the vendored bzip2's: the C sources in
`projects/bzip2/upstream` (commit f5dfc96: 1.0.8 plus later fixes), documented with citations in
`projects/bzip2/docs/surface-area.md`. Do not change `projects/bzip2/upstream` or `projects/bzip2/tools/`; build C
code out of tree. `projects/bzip2/tools/verify.sh` must pass when you finish.
