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

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/oracle/Bzip2/Oracle/Decompress/Huffman.lean`
- `projects/bzip2/oracle/Bzip2/Oracle/Decompress/Huffman/*`
- `projects/bzip2/tests/Bzip2/Test/DecompressHuffman*.lean`
- `projects/bzip2/tests/vectors/decompress-huffman/*`

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
