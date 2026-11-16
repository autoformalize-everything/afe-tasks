# Fuzz: differential decompression

Add `projects/bzip2/fuzz/decompress_diff.c`, a libFuzzer harness that decompresses its input with
libbz2 (`BZ2_bzBuffToBuffDecompress`, one stream, output capped at 16 MiB: treat
`BZ_OUTBUFF_FULL` as "skip") and with the oracle's `decompressStream`, and aborts unless both
give the same verdict (ok / magic / data / eof) and, on ok, the same bytes and the same number of
input bytes consumed.

- `projects/bzip2/fuzz/run.sh decompress SECONDS` builds if needed and runs it on
  `projects/bzip2/fuzz/corpus/decompress/`, seeded from `projects/bzip2/fuzz/corpus/seed/` (commit a small seed
  set: the upstream samples and a few hand-made streams).
- Every disagreement found while doing this is either an oracle bug (fix it here, add the input
  to the golden tests) or a spec question (file a followup for `bzip2/spec/*`).

## Done when

- `projects/bzip2/fuzz/run.sh decompress 120` finds nothing (the task's verify runs it); document in
  `projects/bzip2/fuzz/README.md` how to run longer campaigns and minimise a crash.

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/fuzz/*`
- `projects/bzip2/oracle/Bzip2/Oracle/Decompress.lean`
- `projects/bzip2/oracle/Bzip2/Oracle/Decompress/*`
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
