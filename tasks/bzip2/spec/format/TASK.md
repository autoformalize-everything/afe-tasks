# Spec: stream and block format, and Decodes

Define, in `Bzip2.Spec.Format`, the bit-level syntax of a bzip2 stream and the relation
`Decodes : List UInt8 → List UInt8 → Prop` (or `ByteArray`; be consistent with the other spec
modules), using the other spec modules for CRC, RLE1, BWT, MTF/RLE2 and Huffman.

Cover, as `projects/bzip2/upstream/decompress.c` and `bzlib.c` define them:

- bits: bytes read most significant bit first; n-bit fields big-endian;
- stream header `BZh` and level `1`..`9`; block header magic 0x314159265359, block CRC,
  randomised flag (and the `BZ2_rNums` table in `randtable.c`), 24-bit `origPtr`;
- the symbol map (16 + 16×16 bits), `nGroups` (2..6), `nSelectors` (15 bits, at least 1; the C library
  reads but ignores selectors past 18002), the MTF-coded unary selectors, the delta-coded code
  lengths per group, symbols in groups of 50 each with its selector's table;
- the block's decoded size limit `100000 * level`, the `origPtr < nblock` check, block CRC;
- end-of-stream magic 0x177245385090, the combined CRC, padding to a byte boundary;
- concatenated streams as `bzip2 -d` accepts them (`uncompressStream` in `bzip2.c`), and what
  follows the last stream.

Also define failure: the classes `magic`, `data`, `eof` of `Bzip2.Oracle.Error` (without
importing the oracle: define the spec's own type), and which one the C library reports first for a
given input, since its decoder stops at the first problem.

Finally state the compressor's contract: `ValidCompression level input output` (a stream whose
header has `level`, whose blocks respect the level's size limit, and which decodes to
`input`).

## Done when

- `Decodes`, the failure classes with their order, and `ValidCompression`, all in
  `Bzip2.Spec.Format`, with a module doc that maps each rule to its place in the C sources.
- An `example` decoding a small real stream (made with `bzip2 -c`) to its contents, by `decide`
  or a short proof.

## Ground rules

Read "The Lean formalization" in `projects/bzip2/README.md` first: the layout, the import rules
(the oracle uses no Mathlib and no spec), no `sorry`/`admit`/`axiom`, and the oracle's API in
`projects/bzip2/oracle/Bzip2/Oracle.lean`. Behaviour is the vendored bzip2's: the C sources in
`projects/bzip2/upstream` (commit f5dfc96: 1.0.8 plus later fixes), documented with citations in
`projects/bzip2/docs/surface-area.md`. Do not change `projects/bzip2/upstream` or `projects/bzip2/tools/`; build C
code out of tree. `projects/bzip2/tools/verify.sh` must pass when you finish.
