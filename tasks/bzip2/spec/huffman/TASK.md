# Spec: Huffman code lengths and decoding

Define, in `Bzip2.Spec.Huffman`, the prefix codes bzip2 uses:

- a table is a code length (1..20) per symbol of the alphabet;
- the code bzip2 assigns (`BZ2_hbAssignCodes` in `projects/bzip2/upstream/huffman.c`): by increasing length, and
  within a length by symbol index;
- decoding a symbol from a bit sequence, as `BZ2_hbCreateDecodeTables` and the `GET_MTF_VAL`
  macro in `decompress.c` behave on *every* table the format can carry, including incomplete or
  over-subscribed length sets, which the decoder does not reject up front: say what it decodes
  then and when it fails (`zn > 20`, index out of range).

Prove, or state for `bzip2/proofs/huffman`, that for a complete, prefix-free table decoding the
assigned code of a symbol yields that symbol.

## Done when

- Code assignment and a decoding relation or function matching the C decoder on all tables;
  the module doc lists the malformed-table cases and their outcome.
- `example`s: a small complete table, an incomplete one, an over-subscribed one.
- `projects/bzip2/tools/check-task.sh spec/huffman` passes: `projects/bzip2/checks/spec/huffman.lean` lists the declarations this task must provide, under the names other tasks import, and the examples that must hold.

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/spec/Bzip2/Spec/Huffman.lean`
- `projects/bzip2/spec/Bzip2/Spec/Huffman/*`

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
