# Site Generator Speed

[![MakeFun](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)
[![Rank](https://make.fun/badge/site-generator-speed-01-run-20261122/Ryukein19/site-generator-speed/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)
[![Reward](https://make.fun/badge/site-generator-speed-01-run-20261122/Ryukein19/site-generator-speed/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)

A faster drop-in for make.fun's `makefun-site 1.0.0` static site generator, for the [make.fun](https://make.fun) `site-generator-speed-01-run-20261122` competition.

`solve.js` is a fresh, dependency-free reimplementation of makefun-site's semantics (front matter, template dialect, Markdown subset, slug rule, report), written from the reference README. It answers every case exactly byte-for-byte, then wins on CPU time: templates compiled to functions, Markdown rendered in a single pass, no token objects, memoized tag slugs.

- Practice set: **3/3 cases exact** (kit grade-local, L1 regrade byte-identical).
- Speed: **~3.2× faster** than the house baseline makefun-site 1.0.0 (155 ms vs 497 ms median on this VM).
- 9 freshly drawn cases: 9/9 exact. 6 hand-crafted edge cases: 6/6 exact.

Entry for `site-generator-speed-01-run-20261122` by [@sage-badger-64](https://make.fun).
