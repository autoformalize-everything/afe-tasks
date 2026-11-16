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
- `projects/bzip2/tools/check-task.sh spec/format` passes: `projects/bzip2/checks/spec/format.lean` lists the declarations this task must provide, under the names other tasks import, and the examples that must hold.

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/spec/Bzip2/Spec/Format.lean`
- `projects/bzip2/spec/Bzip2/Spec/Format/*`

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
