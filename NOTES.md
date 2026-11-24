# site-generator-speed-01-run-20261122 — entry notes

Entry: a fresh, dependency-free reimplementation of makefun-site 1.0.0
(`solve.js`, ES module, `run(input)` returns the output tree; `main.ts`
is the harness driver). Written from the makefun-site README and its
reference semantics — no other entrant's code was read or copied.

## Practice-set exactness (kit `practice/`, 3 cases)

| case | files | result |
| ---- | ----- | ------ |
| g-0001 | 257 (250 md) | EXACT |
| g-0002 | 348 (340 md) | EXACT |
| g-0003 | 486 (480 md) | EXACT |

Checked by canonical JSON comparison (the scorer's rule: same type,
strings code unit for code unit, arrays in order, objects by key set),
against `practice/reference/expected.json`. `main.ts` run end-to-end
(`MAKEFUN_INPUT_DIR`/`MAKEFUN_OUTPUT_DIR`) also writes 3/3 exact
`answers.json`.

## Extra validation (beyond the practice set)

- 9 cases drawn from the kit's own generator (`subject/sites.mjs`) with
  3 fresh seeds (9 case draws, 200–600 sources each): **9/9 exact**,
  and re-running `run` on the same input is byte-identical
  (deterministic, as the harness requires).
- 6 hand-crafted edge cases, all exact: every front-matter refusal
  reason (no/unclosed front matter, bad line, unknown/duplicate/empty
  key, bad date/tags/draft/layout/slug, missing title, bad quoted
  string), reserved dirs (`tags/`, `page/`), post with no date, missing
  layout, drafts, Markdown torture (unclosed fence/strong/em/code/link,
  nested `**a *b* c**`, `***triple***`, setext underlines, indented
  code, escapes, every link kind), Unicode slugs + collisions
  (Café/cafe/CAFE!, Łódź, Ærøskøbing, ﬁnal, Ｗｉｄｅ), CRLF sources,
  root reserved slugs, empty site, missing index/tag templates, static
  shadowing, nested loops with `loop.index/first/last/length`, and an
  include chain 9 deep (depth cap 8).

## Timing (this VM, Node 24.22.1, 3 practice cases, median of 10 rounds)

| | time | |
| --- | --- | --- |
| mine | 155 ms | |
| house baseline (makefun-site 1.0.0) | 497 ms | |
| ratio | **3.20x faster** | |

Per case (mine, median): g-0001 29.5 ms, g-0002 56.6 ms, g-0003 57.4 ms.
On the 9 freshly drawn cases the ratio was 2.42x (that loop also timed
the case generator itself, which is shared work, so it understates the
run-only ratio).

## How the speed is won (exactness preserved)

- Templates compiled to functions (`new Function`) instead of walking
  a node tree per render; paths collected once per template (no path
  array per lookup); a for loop reuses one scope object and one `loop`
  object across its elements (bodies run synchronously).
- Markdown rendered in one pass with no token objects; block dispatch
  by first character instead of 7 regexes per line.
- Inline rendering scans with one regex and copies plain runs verbatim
  in a single slice (a run holds no syntax or escape character, so it
  needs no escaping); escape/strong/em/link closers keep the
  reference's exact search rules.
- `slugify` skips NFKD + mark-stripping for pure-ASCII text (provably
  identical: NFKD is the identity on ASCII, ASCII has no combining
  marks); tag slugs are memoized per build.
- `wordsOf` counts by one scan using exactly JavaScript's `\s` set
  (ASCII whitespace + U+00A0, U+1680, U+2000–U+200A, U+2028, U+2029,
  U+202F, U+205F, U+3000, U+FEFF) — no match array is allocated.
- Front matter is scanned with `indexOf` (no split/join of the whole
  source); the body is one slice. Line-ending normalisation is skipped
  when the source holds no `\r`.
- `trimLine` is a charCode trim (spaces/tabs only), not a regex.

## Known gaps / risks

- None known on exactness: practice 3/3, 9 fresh draws 9/9, 6 edge
  cases 6/6. The one semantic area the practice distribution exercises
  least is pathological template nesting (very deep for/if trees) —
  the compiler handles arbitrary nesting (unique locals per node), and
  the include-depth edge case is covered above.
- `main.ts` keeps the house driver's `--no-incremental-marking` V8
  flag, as the rules allow.
- Repo size is far under the 4 MiB cap (solve.js ~30 KB, main.ts ~1.2 KB).
