# Proof: the BWT, forward and inverse

In `Bzip2.Proofs.Bwt`, prove:

- the oracle's inverse (`cftab`/`tt` walk) computes the spec's inverse on valid inputs and fails
  exactly on invalid ones;
- the oracle's forward transform yields the spec's transform, with an `origPtr` that points at
  the block;
- hence inverse after forward is the identity on every block.

## Done when

- The theorems, with no `sorry`, in their module under `Bzip2.Proofs`. The inverse direction first: it is
  what decompression needs.
- `projects/bzip2/tools/check-task.sh proofs/bwt` passes: `projects/bzip2/checks/proofs/bwt.lean` lists the declarations this task must provide, under the names other tasks import, and the examples that must hold.

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/proofs/Bzip2/Proofs/Bwt.lean`
- `projects/bzip2/proofs/Bzip2/Proofs/Bwt/*`

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
