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

## Critical: this repo does NOT contain the library

`rs-grid-*` and `example-common` are **git dependencies pinned to a tag** (currently
`rs-grid-core-v0.1.3`, see [`Cargo.toml`](Cargo.toml)):

- The library source is in the separate `rs-grid` repo. Editing files here changes only
  the demo wiring in `src/lib.rs` — never grid behaviour.
- **All four deps must share the exact same tag.** Mixing per-crate tags breaks the build
  (`example-common` must match the library it was built against).
- To adopt a new library version: bump the tag on all four deps together, then `cargo update`.

## Conventions

- `themes/` is **vendored** from the rs-grid reference theme — re-vendor rather than hand-edit.
- `generated/` is build output — never hand-edit.
- Rust files are auto-formatted on save (PostToolUse `rustfmt` hook). No clippy hook: this
  is a `cdylib` + `wasm-bindgen` crate, so host-target clippy does not apply.
- This example also has its own Playwright `e2e/` suite.
