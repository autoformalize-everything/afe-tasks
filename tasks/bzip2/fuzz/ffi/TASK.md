# Fuzz: the oracle as a C library

Make the oracle callable from C, for the libFuzzer harnesses:

- a Lean library (a `lean_lib` with a static-library facet in `projects/bzip2/lakefile.toml`, sources in
  `projects/bzip2/fuzz/ffi/`) with `@[export]` functions over `Bzip2.Oracle.decompress`,
  `decompressStream` and `compress`, taking and returning byte buffers and an error code that
  maps the oracle's `Error` to libbz2's codes (`BZ_DATA_ERROR_MAGIC`, `BZ_DATA_ERROR`,
  `BZ_UNEXPECTED_EOF`, `BZ_PARAM_ERROR`, and a code of your own for `unimplemented`);
- `projects/bzip2/fuzz/ffi/bzip2_oracle.h`, and Lean runtime initialisation done once per process;
- `projects/bzip2/fuzz/build.sh`: builds libbz2 from a copy of `projects/bzip2/upstream` (read-only) with clang and
  `-fsanitize=address,undefined,fuzzer-no-link`, builds the Lean static library, and links a
  smoke test `projects/bzip2/fuzz/build/ffi_smoke` that calls each entry point once and exits 0.

Builds go to `projects/bzip2/fuzz/build/` (ignored by git).

## Done when

- `projects/bzip2/fuzz/build.sh && projects/bzip2/fuzz/build/ffi_smoke` succeeds in the project image (the task's
  verify runs it), with the oracle's current stubs or real code.

## Ground rules

Read "The Lean formalization" in `projects/bzip2/README.md` first: the layout, the import rules
(the oracle uses no Mathlib and no spec), no `sorry`/`admit`/`axiom`, and the oracle's API in
`projects/bzip2/oracle/Bzip2/Oracle.lean`. Behaviour is the vendored bzip2's: the C sources in
`projects/bzip2/upstream` (commit f5dfc96: 1.0.8 plus later fixes), documented with citations in
`projects/bzip2/docs/surface-area.md`. Do not change `projects/bzip2/upstream` or `projects/bzip2/tools/`; build C
code out of tree. `projects/bzip2/tools/verify.sh` must pass when you finish.
