# bzip2 tasks

`surface-area` (done) documented bzip2's external surface in `projects/bzip2/docs/` of afe-oss.
The rest formalize it in Lean, in `projects/bzip2/` there (see "The Lean formalization" in its
README):

- **spec**: what a stream is and what it decodes to (`Bzip2.Spec`, declarative, Mathlib);
- **oracle**: the executable Lean implementation (`Bzip2.Oracle`, core + Std only);
- **proofs**: the oracle meets the spec (`Bzip2.Proofs`);
- **fuzz**: libFuzzer harnesses holding libbz2 and the oracle to the same behaviour;
- **tests**: golden vectors from the vendored bzip2, run by `lake test`.

Ground truth is the vendored source, `projects/bzip2/upstream` (f5dfc96: 1.0.8 plus later
fixes). Every formalization task's verify is `projects/bzip2/tools/verify.sh` (Lake packages,
`lake build` with no `sorry`, source rules, `lake test`); the fuzz tasks add a timed fuzzing run.

## Order

Claimable at once (no deps): every `spec/*` part except `format`, `oracle/bitio`, `oracle/crc`,
`oracle/compress/{bwt,mtf-rle2,huffman}`, `tests/golden`, `fuzz/ffi`. The oracle's API exists as
stubs, so the CLI, tests and FFI work against it before the oracle does.

```
spec/{crc,rle1,bwt,mtf-rle2,huffman} ──────────────────────────────► spec/format
oracle/bitio ─► decompress/huffman ─► decompress/block-header ─► decompress/block-data
oracle/crc ──────────────────────────────────────────────────────► decompress/block-output
                                                                    └► decompress/stream
oracle/crc ─► compress/rle1-blocks ─┐
compress/{bwt,mtf-rle2,huffman} ────┼─► compress/stream ─► compress/byte-exact
oracle/bitio ───────────────────────┘
fuzz/ffi + decompress/stream ─► fuzz/decompress-diff
fuzz/ffi + compress/stream + decompress/stream ─► fuzz/compress-diff
spec part + oracle part ─► proofs/{crc,rle,bwt,huffman} ─► proofs/{decompress,compress}
```

Priorities put the spec (90) and the golden tests (86) first, then the oracle from the bit
reader up (85 → 68), the fuzzers (65 → 60), the proofs (60 → 55), and byte-identical compression
last (40). Proof tasks run at `max` effort.

The image `afe-bzip2-lean:dev` is built on each worker host from afe-oss
`images/bzip2-lean/Dockerfile` (see the comment at its top). The tasks expect afe-fleet's
container runtime and its `prepare` support.
