# Proof: Huffman tables

In `Bzip2.Proofs.Huffman`, prove:

- the oracle's decoding tables decode exactly as the spec's decoding, on every table the format
  can carry (including malformed ones);
- the code lengths the compressor chooses are in 1..17 and form a complete-or-under-full prefix
  code, so encoding then decoding a symbol sequence returns it.

## Done when

- The theorems, with no `sorry`, imported by `Bzip2.Proofs`.

## Ground rules

Read "The Lean formalization" in `projects/bzip2/README.md` first: the layout, the import rules
(the oracle uses no Mathlib and no spec), no `sorry`/`admit`/`axiom`, and the oracle's API in
`projects/bzip2/oracle/Bzip2/Oracle.lean`. Behaviour is the vendored bzip2's: the C sources in
`projects/bzip2/upstream` (commit f5dfc96: 1.0.8 plus later fixes), documented with citations in
`projects/bzip2/docs/surface-area.md`. Do not change `projects/bzip2/upstream` or `projects/bzip2/tools/`; build C
code out of tree. `projects/bzip2/tools/verify.sh` must pass when you finish.
