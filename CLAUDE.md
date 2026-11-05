# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Single-page, printable portfolio/CV built with Astro 4 + TypeScript (strict). Based on the `midudev/minimalist-portfolio-json` template. No tests, no linter, no UI framework, no CSS framework — plain scoped `<style>` blocks per component.

## Commands

pnpm is the intended package manager (both `pnpm-lock.yaml` and `package-lock.json` exist; keep them in sync or prefer pnpm). Node `24.x` is required by `engines`.

- `pnpm dev` / `pnpm start` — dev server at http://localhost:4321
- `pnpm build` — runs `astro check` (type-check, acts as the only "lint") then `astro build` into `dist/`
- `pnpm preview` — serve the production build

## Architecture

**All content lives in `src/cv.json`** (JSON Resume schema). Changing the portfolio content means editing this file only; components are pure renderers of it.

- `src/pages/index.astro` composes the page from `src/components/sections/*` inside `src/layouts/Layout.astro`. `Education.astro` exists but is not mounted. Sections for `volunteer`, `awards`, `certificates`, `publications`, `interests`, `references`, `languages` do not exist — those keys in `cv.json` are template placeholders and are never rendered.
- Each section imports its slice directly via `import { work } from "@/cv.json"`, so types are inferred from the JSON. Adding/removing a field or making it `null` in some entries changes the inferred type and can break `astro check` (e.g. `Experience` handles a missing `endDate` as "Present").
- `@/*` maps to `src/*` (`tsconfig.json`).
- Social icons are resolved by `profiles[].network` name: `Hero.astro` maps to components in `src/icons/`, and `KeyboardManager.astro` has its own inline SVG map. A new network must be added in both places or it renders without an icon.
- `KeyboardManager.astro` wires the `ninja-keys` web component (ctrl+k palette): a Print action plus one entry per profile with hotkey `ctrl+<first letter of network>`.
- Styling uses CSS custom properties defined in `Layout.astro` (`--bg`, `--text-muted`, `--accent`, …) with a `prefers-color-scheme: dark` override and a print override; components consume tokens instead of hardcoded colors. The shared `.tag` class styles every chip.
- Print is a first-class output: `.print` / `.no-print` classes and `@media print` rules toggle what appears in the printed CV.
