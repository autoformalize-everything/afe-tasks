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

## Ground rules

Read "The Lean formalization" in `projects/bzip2/README.md` first: the layout, the import rules
(the oracle uses no Mathlib and no spec), no `sorry`/`admit`/`axiom`, and the oracle's API in
`projects/bzip2/oracle/Bzip2/Oracle.lean`. Behaviour is the vendored bzip2's: the C sources in
`projects/bzip2/upstream` (commit f5dfc96: 1.0.8 plus later fixes), documented with citations in
`projects/bzip2/docs/surface-area.md`. Do not change `projects/bzip2/upstream` or `projects/bzip2/tools/`; build C
code out of tree. `projects/bzip2/tools/verify.sh` must pass when you finish.
