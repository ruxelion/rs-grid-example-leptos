# rs-grid-example-leptos

The full-featured showcase of [rs-grid](https://github.com/ruxelion/rs-grid)
with **Leptos 0.8 (CSR)**, [Trunk](https://trunkrs.dev) and Tailwind/DaisyUI:
dataset-size / column-count / theme / language selectors, editable & selectable
toggles, and column-layout persistence.

This example pins the library at a released tag (`v0.1.0`) via a git
dependency — see [`Cargo.toml`](Cargo.toml).

## Prerequisites

```sh
rustup target add wasm32-unknown-unknown
cargo install trunk --locked
# Node.js 20+ (for the Tailwind build); installed automatically on first build
```

## Develop

```sh
trunk serve     # http://localhost:9080 (hot-reload)
```

A Trunk `pre_build` hook ([`scripts/build-css.mjs`](scripts/build-css.mjs))
runs `npm install` (first time) then compiles Tailwind to
`generated/tailwind.css` before each build.

## Build

```sh
trunk build --release   # → dist/
```

## Layout

| Path | Role |
| --- | --- |
| `src/lib.rs` | Leptos app (`App` component + layout persistence) |
| `input.css` | Tailwind v4 entry (`@import "tailwindcss"; @plugin "daisyui"`) |
| `scripts/build-css.mjs` | Trunk pre-build hook (Tailwind compile) |
| `themes/` | rs-grid theme CSS variables (vendored) |

The grid theme is defined by the CSS variables in [`themes/`](themes/) (vendored
from the rs-grid reference theme).
