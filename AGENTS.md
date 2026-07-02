# rs-grid-example-leptos — Claude guide

The **full-featured showcase** of **rs-grid** with **Leptos 0.8 (CSR)** + Trunk +
Tailwind/DaisyUI: dataset-size / column-count / theme / language selectors, editable &
selectable toggles, column-layout persistence. App logic: [`src/lib.rs`](src/lib.rs).
Full build/run notes: [`README.md`](README.md).

## Quick reference

```sh
trunk serve            # dev → http://localhost:9080 (hot-reload)
trunk build --release  # → dist/
```

A Trunk `pre_build` hook ([`scripts/build-css.mjs`](scripts/build-css.mjs)) runs
`npm install` (first time) then compiles Tailwind v4 to `generated/tailwind.css` before
each build. `input.css` is the Tailwind entry (`@import "tailwindcss"; @plugin "daisyui"`).

## Before coding

<!-- keep in sync with the "Before coding" section in every other repo's AGENTS.md -->
**Plan before coding non-trivial changes.** For a bug fix or feature that
touches more than one file, changes a public API/component contract, or
isn't an obvious one-liner, propose a short plan (approach, files touched,
trade-offs) before writing code — use Claude Code's Plan Mode rather than
diving straight into edits. Skip this for trivial fixes; planning every
one-line change only adds friction.

## Critical: this repo does NOT contain the library

<!-- keep in sync with rs-grid/AGENTS.md "How they relate" + the other 3
     rs-grid-example-*/AGENTS.md "Critical" sections -->

`rs-grid-*` and `example-common` are **git dependencies pinned to a tag** — see
the `tag =` value in [`Cargo.toml`](Cargo.toml) for the current pin (do not
hardcode a version/tag name in prose here, it goes stale):

- The library source is in the separate `rs-grid` repo. Editing files here changes only
  the demo wiring in `src/lib.rs` — never grid behaviour.
- **All four deps must share the exact same tag.** Mixing per-crate tags breaks the build
  (`example-common` must match the library it was built against).
- To adopt a new library version: bump the tag on all four deps together, then `cargo update`.

## End-to-end tests (Playwright)

A functional + visual-regression suite ([`e2e/`](e2e/)) covers every parity feature
(selectors, toggles, persistence, canvas interaction).

```sh
cd e2e && npm install              # first time
npx playwright install chromium    # first time
cd .. && trunk build               # build dist/ first
cd e2e && npm test                 # run
cd e2e && npm run update-snapshots # regenerate visual baselines
```

CI (`.github/workflows/ci.yml`, `e2e` job) runs this automatically on every
push/PR, but scoped to `tests/grid.spec.ts` only and with
`--update-snapshots --grep-invert "visual regression|colonnes pinnées|
précision f64"` — `editing.spec.ts` / `csp.spec.ts` / `leptos-component.spec.ts`
aren't yet verified stable on a Linux runner, and screenshots always write
fresh rather than diff against a baseline (see Conventions below: no visual
baselines are committed here).

## Conventions

- `themes/` is **vendored** from the rs-grid reference theme — re-vendor rather than hand-edit.
- `generated/` is build output — never hand-edit.
- Rust files are auto-formatted on save (PostToolUse `rustfmt` hook, then a blocking
  `cargo check --target wasm32-unknown-unknown`). No clippy hook: this is a `cdylib` +
  `wasm-bindgen` crate, so host-target clippy does not apply.
- Formatting uses stable `rustfmt` defaults (no `rustfmt.toml` here, unlike `rs-grid`'s
  nightly-only config) — intentional, so this demo never requires a nightly toolchain.
- No `unwrap()` in production code — use `expect("reason")` or error propagation.
- English (US) only in code, comments, and strings.
- In `e2e/`, `node_modules/` and `test-results/` are gitignored. Unlike its 3
  sibling examples, `tests/snapshots/` is **also gitignored here** (root
  `.gitignore`) — no visual baselines are committed, so CI and local runs
  always regenerate them fresh (`--update-snapshots`) rather than diffing
  against a reference image.

## Public surface (keep in sync with `README.md`)

The selectors/toggles wired to `rs_grid_leptos::GridCanvas` in
[`src/lib.rs`](src/lib.rs) (dataset size, column count, theme, language,
editable/selectable, layout persistence) are this demo's public-facing
contract. If you add, remove, or rename one, update `README.md`'s feature
list in the same commit.
