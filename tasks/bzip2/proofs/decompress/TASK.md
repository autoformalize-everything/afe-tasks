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

- The three theorems, with no `sorry`, in `Bzip2.Proofs.Decompress`, or a partial result with
  followups as described.
- `projects/bzip2/tools/check-task.sh proofs/decompress` passes: `projects/bzip2/checks/proofs/decompress.lean` lists the declarations this task must provide, under the names other tasks import, and the examples that must hold.

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/proofs/Bzip2/Proofs/Decompress.lean`
- `projects/bzip2/proofs/Bzip2/Proofs/Decompress/*`

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
