# Document the external surface of bzip2

Write a complete, precise inventory of everything that code or people outside bzip2 can touch:
every function, symbol, type, macro, program and byte format, with its exact contract. This is
the starting point for specifying and formally verifying bzip2, so precision matters more than
prose.

- **Source:** `projects/bzip2/upstream/`: bzip2 at upstream commit `f5dfc96` (1.0.8 plus later
  fixes), unmodified. Do not edit it.
- **Write:** `projects/bzip2/docs/surface-area.md` and `projects/bzip2/docs/surface-area.json`.
  Change nothing else.
- **Check:** `python3 projects/bzip2/tools/check_surface.py --only-docs` must pass. It derives the
  required inventory from the source and checks coverage only; review checks correctness.

## What counts as surface

1. **Public C API:** the 24 functions declared with `BZ_API` in `bzlib.h`.
2. **Exported internals:** global functions and data that `libbz2` defines but `bzlib.h` does not
   declare (for example `BZ2_blockSort`, `BZ2_crc32Table`). Programs can link against them. Say
   what each is, and that it is not part of the public API.
3. **Types:** `bz_stream` (every field: who sets it, who reads it, when, and what it means
   between calls) and `BZFILE`.
4. **Macros:** action codes, return codes, `BZ_MAX_UNUSED`, and the configuration macros
   (`BZ_NO_STDIO`, `BZ_IMPORT`, `BZ_EXPORT`, `BZ_API`, `BZ_EXTERN`) with what each one changes.
5. **Programs** installed by `make install`: `bzip2` and its aliases, `bzip2recover`, and the `bz*`
   scripts. Options, exit codes, files read and written, environment variables (`BZIP2`, `BZIP`).
6. **The .bz2 format** as the library reads and writes it: stream header, block header, block
   contents at the level the code implements them, end-of-stream marker, CRCs. Describe what the
   code does; do not re-derive the algorithm.

## For every function

- the exact signature, as in the source
- what it does
- each parameter: meaning, valid values (for example `blockSize100k` 1–9, `verbosity` 0–4,
  `workFactor` 0–250, `small` 0 or 1), ownership and lifetime of pointers
- every return value or error code it can produce, and exactly when
- preconditions (which state the stream or handle must be in), postconditions, and the state
  transition it causes
- side effects: allocation through `bzalloc`/`bzfree`/`opaque`, I/O, global state, output on
  stderr (`verbosity`), paths that end in `BZ2_bz__AssertH__fail`
- thread safety and reentrancy, as far as the source shows
- whether it exists under `BZ_NO_STDIO`

Ground every non-obvious claim in the source with a `file:line` citation. Where `manual.xml`
and the code disagree, the code wins: note the discrepancy. Mark anything you are unsure of as
uncertain rather than guessing.

## surface-area.md

1. Overview: how the pieces fit (low-level stream API, high-level `BZFILE` API, one-shot
   buffer functions, zlib-style compatibility functions, internals).
2. The stream API as a state machine, for compression and for decompression: the states,
   which call is legal in which state, and which transition each return code means (when
   `BZ_SEQUENCE_ERROR` is returned).
3. One section per function, type, macro group and program.
4. Exported internals.
5. The file format.
6. Discrepancies between the manual and the code, and open questions.

## surface-area.json

```json
{
  "upstream_commit": "f5dfc96063fef2d9c5446d0733867d899ef9b942",
  "functions": [{"name": "", "signature": "", "public": true, "summary": "",
                 "parameters": [{"name": "", "type": "", "meaning": "", "valid": ""}],
                 "returns": "", "errors": [{"code": "", "when": ""}],
                 "preconditions": [], "postconditions": [], "side_effects": [],
                 "no_stdio": false, "source": "bzlib.c:123"}],
  "data": [{"name": "", "type": "", "summary": "", "source": ""}],
  "types": [{"name": "", "summary": "",
             "fields": [{"name": "", "type": "", "set_by": "", "read_by": "", "meaning": ""}]}],
  "macros": [{"name": "", "value": "", "kind": "action|return|limit|config", "summary": ""}],
  "programs": [{"name": "", "summary": "", "options": [], "exit_codes": [], "environment": []}]
}
```

The checker requires `name`, `signature`, `public`, `summary`, `parameters` and `returns` for
functions, and `name` and `summary` for everything else. Review expects the other fields too.

## Out of scope

Changes to `upstream/` or `tools/`, tests, fuzzing, formal specifications. Those are later tasks.
