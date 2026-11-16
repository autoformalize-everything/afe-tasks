# Proof: Huffman tables

In `Bzip2.Proofs.Huffman`, prove:

- the oracle's decoding tables decode exactly as the spec's decoding, on every table the format
  can carry (including malformed ones);
- the code lengths the compressor chooses are in 1..17 and form a complete-or-under-full prefix
  code, so encoding then decoding a symbol sequence returns it.

## Done when

- The theorems, with no `sorry`, in their module under `Bzip2.Proofs`.
- `projects/bzip2/tools/check-task.sh proofs/huffman` passes: `projects/bzip2/checks/proofs/huffman.lean` lists the declarations this task must provide, under the names other tasks import, and the examples that must hold.

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/proofs/Bzip2/Proofs/Huffman.lean`
- `projects/bzip2/proofs/Bzip2/Proofs/Huffman/*`

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
