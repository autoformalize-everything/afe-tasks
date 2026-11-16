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

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/fuzz/*`
- `projects/bzip2/oracle/Bzip2/Oracle/Compress.lean`
- `projects/bzip2/oracle/Bzip2/Oracle/Compress/*`
- `projects/bzip2/tests/vectors/*`

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
