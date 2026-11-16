# Proof: RLE1 and MTF/RUNA-RUNB

In `Bzip2.Proofs.Rle`, prove:

- the oracle's RLE1 decoding equals the spec's on every input, and the oracle's block splitting
  with RLE1 encoding decodes back to the input;
- the oracle's MTF/RUNA-RUNB decoding equals the spec's (same result, same failures), and its
  encoding decodes back to the BWT output.

If the oracle's in-place arrays make a direct proof heavy, prove it equal to a functional
version first, then that version against the spec.

## Done when

- The theorems, with no `sorry`, in their module under `Bzip2.Proofs`. If part of it is out of reach in
  one attempt, finish the decoding half and file the rest as a followup.

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
