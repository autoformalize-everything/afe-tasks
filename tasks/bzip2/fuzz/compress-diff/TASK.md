# Fuzz: differential compression

Add `projects/bzip2/fuzz/compress_diff.c`, a libFuzzer harness that takes a level (from the first input
byte) and data, compresses with libbz2 (`BZ2_bzBuffToBuffCompress`, `workFactor` 0 meaning 30)
and with the oracle, and aborts unless:

- libbz2 decompresses the oracle's output to the data, and the oracle decompresses libbz2's
  output to the data;
- (behind a compile-time switch, off until `oracle/compress/byte-exact` is done) the two
  outputs are byte-identical.

`projects/bzip2/fuzz/run.sh compress SECONDS` runs it as for decompression.

## Done when

- `projects/bzip2/fuzz/run.sh compress 120` finds nothing with the roundtrip checks (the task's verify
  runs it).

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
