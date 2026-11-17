# Fuzz: differential decompression

Add `projects/bzip2/fuzz/decompress_diff.c`, a libFuzzer harness that decompresses its input with
libbz2 (`BZ2_bzBuffToBuffDecompress`, one stream, output capped at 16 MiB: treat
`BZ_OUTBUFF_FULL` as "skip") and with the oracle's `decompressStream`, and aborts unless both
give the same verdict (ok / magic / data / eof) and, on ok, the same bytes and the same number of
input bytes consumed.

- `projects/bzip2/fuzz/run.sh decompress SECONDS` builds if needed and runs it on
  `projects/bzip2/fuzz/corpus/decompress/`, seeded from `projects/bzip2/fuzz/corpus/seed/` (commit a small seed
  set: the upstream samples and a few hand-made streams).
- Before it aborts, the harness prints one line to stderr naming the disagreement in words that
  do not depend on the input (no sizes, offsets or counts): `AFE-FUZZ: verdict libbz2=<v>
  oracle=<v>`, `AFE-FUZZ: output length differs`, `AFE-FUZZ: output differs`,
  `AFE-FUZZ: consumed differs`, `AFE-FUZZ: unexpected status <name>`. The fuzz runner
  (`afe-fleet fuzz run`) groups what it finds by this line.
- Fixing disagreements is not this task's job: the fuzz runner runs the campaign on `master`
  and files each kind it finds as its own task, under `tasks/bzip2/fuzz-findings/decompress/`.
  Fixes already on this task's branch may stay.

## Done when

- The harness builds and agrees on every input in `projects/bzip2/fuzz/corpus/seed/`:
  `projects/bzip2/fuzz/run.sh decompress 60 -runs=0` exits 0 (the task's verify runs it).
- `projects/bzip2/fuzz/README.md` says how to run a campaign by hand and minimise a crash, and
  that the standing campaign is `tasks/bzip2/fuzz.toml`'s, run by `afe-fleet fuzz run`.

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
