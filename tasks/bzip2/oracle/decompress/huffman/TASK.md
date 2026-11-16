# Oracle: Huffman decoding tables

Add `Bzip2.Oracle.Decompress.Huffman`: build decoding tables from code lengths and decode one
symbol from a `BitIO` reader, behaving exactly as `BZ2_hbCreateDecodeTables` (`huffman.c`) and
`GET_MTF_VAL` (`decompress.c`) do, including on incomplete and over-subscribed length sets and
the failures (`zn > 20`, index out of range → `Error.data`).

Keep the C structure (`limit`, `base`, `perm`, `minLen`) unless a faster table gives the same
results on every input; `bzip2/proofs/huffman` will relate it to the spec.

## Done when

- Tests: tables from real streams, an incomplete table, an over-subscribed one, and the failure
  cases, each with the result the C code gives (derive by reading the code or by a small C
  program against `projects/bzip2/upstream`).

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
