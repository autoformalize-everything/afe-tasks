# Oracle: table-driven CRC

Add `Bzip2.Oracle.Crc`: the CRC bzip2 uses, computed with a 256-entry table like
`projects/bzip2/upstream/crctable.c` (generate the table in Lean rather than pasting it), over `ByteArray` slices,
plus the combined stream CRC (`rotl 1` then xor).

## Done when

- `crc32 "123456789" = 0xFC891918`, the empty input gives 0, and the table equals
  `BZ2_crc32Table` (a test compares a few entries with the C file).
- `projects/bzip2/tools/check-task.sh oracle/crc` passes: `projects/bzip2/checks/oracle/crc.lean` lists the declarations this task must provide, under the names other tasks import, and the examples that must hold.

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/oracle/Bzip2/Oracle/Crc.lean`
- `projects/bzip2/oracle/Bzip2/Oracle/Crc/*`
- `projects/bzip2/tests/Bzip2/Test/Crc*.lean`

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
