# Spec: symbol map, move-to-front, RUNA/RUNB

Define, in `Bzip2.Spec.MtfRle2`, the steps between the BWT output and the Huffman symbols:

- the symbol map: the set of byte values used in the block (`inUse`), and the alphabet
  `nInUse + 2` (RUNA = 0, RUNB = 1, MTF indices 1.. shifted by one, EOB = nInUse + 1);
- move-to-front over the used bytes, in increasing byte order initially;
- the zero-run code: a run of n zeros (MTF index 0) as n in bijective base 2, digits RUNA (1)
  and RUNB (2), least significant first;
- decoding: from the symbol sequence up to EOB back to bytes, as `getAndMoveToFrontDecode` in
  `projects/bzip2/upstream/decompress.c` does, including its limits (a run longer than the block allows, a block
  longer than `100000 * level`) as failures.

Encoding follows `generateMTFValues` in `compress.c`.

## Done when

- Encoding and decoding definitions; decoding returns `Option` (or an error type) and fails
  exactly where the C library returns `BZ_DATA_ERROR`, listed in the module doc.
- `example`s for runs of 1 to 6 zeros and a short block with three distinct bytes.

## Files

This task may add and change only (`*` crosses directories):

- `projects/bzip2/spec/Bzip2/Spec/MtfRle2.lean`
- `projects/bzip2/spec/Bzip2/Spec/MtfRle2/*`

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
