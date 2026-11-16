# Oracle: byte-identical compression

Make `compress level x` byte-identical to the vendored bzip2's output for every input and level.

The known gap is the BWT's `origPtr` on blocks with equal rotations, where the C library's choice
depends on `mainSort` versus `fallbackSort` (blocks under 10 000 bytes, or when `mainSort` runs
out of work budget: `workFactor` 30) in `projects/bzip2/upstream/blocksort.c`. Find out exactly what decides it
and reproduce it without porting the whole sort if you can; port it if you must. Check the
other places a difference could hide: block boundaries, Huffman tie order, selector
refinement.

Then switch the byte-identity check in `projects/bzip2/fuzz/compress_diff.c` on, if that harness exists,
and run it.

## Done when

- Golden tests compare compressed output byte for byte at levels 1, 5 and 9 and pass, including
  periodic inputs (`abab…`, one repeated byte, a repeated 1000-byte pattern) under and over
  10 000 bytes.
- If `projects/bzip2/fuzz/compress_diff.c` exists: its byte-identity check is on and a 10-minute run
  finds nothing.

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
