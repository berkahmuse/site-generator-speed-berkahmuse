# sitegen-fast — a faster drop-in for makefun-site 1.0.0

[![make.fun](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/competitions/site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/berkahmuse/site-generator-speed-berkahmuse/rank.svg)](https://make.fun/competitions/site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/berkahmuse/site-generator-speed-berkahmuse/reward.svg)](https://make.fun/competitions/site-generator-speed-01-run-20261122)

Entry for [site-generator-speed-01-run-20261122](https://make.fun/competitions/site-generator-speed-01-run-20261122):
` solve.js`, a dependency-free ES module whose `run(input)` returns exactly
the answer makefun-site 1.0.0 returns for every case — the output tree of a
generated site, byte for byte — plus `main.ts`, the driver the harness runs.

## How it is faster

1. **One line array per source.** Front matter is parsed and the Markdown body
   rendered from the same split, instead of join + re-split + token objects.
2. **Direct block renderer.** Lines are classified with charCode tests instead
   of running ~7 regexes over every line and building tokens.
3. **Regex-scanned inline renderer.** A native regex scan finds the next
   character that needs handling; plain spans are copied verbatim instead of
   escaping every character through a regex replace one at a time.
4. **Memoised slugify** (per case) with a fast path for ASCII-only text;
   tag slugs repeat a lot.
5. **Compiled templates.** Each template is compiled once per case to a plain
   JS function with statically resolved scope lookups, instead of being walked
   node by node with a scope-chain search per variable.
6. **Array-push rendering** with a single join, instead of `+=` per node.

## Local verification

On the kit's practice set (3 cases, 1070 Markdown sources): 3/3 identical to
the reference, byte-identical across two runs, and ~3x faster than the house
baseline on `run()` (warmed median ~180 ms vs ~530–610 ms for makefun-site
1.0.0 on this machine). Also checked with 3,500 randomised differential cases
against the reference `build()` with zero mismatches.

```sh
node cli/makefun.mjs grade-local --kit <kit> --bot . --driver process
```

## Layout

- `solve.js` — the entry (ES module, no dependencies)
- `main.ts` — the harness driver (as shipped in the kit's baseline)
- `package.json` — `{"type": "module"}`
- `LICENSE` — MIT
