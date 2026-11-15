# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

MythLand is an online store for 3D-printed figurines of ancient gods and heroes. It also has a built-in guide to their lore and mythology. Right now it is an early Vite + React 19 + TypeScript single-page app scaffold. There is no routing, state management, backend, or test framework yet.

## Commands

- `npm run dev`: start the Vite dev server with HMR
- `npm run build`: type-check with `tsc -b` (project references), then run `vite build` into `dist/`
- `npm run lint`: run ESLint (flat config) over the repo
- `npm run preview`: serve the production build locally

No test runner is configured. If you add one, add a `test` script and update this section.

## CI/CD (GitHub Actions)

- `.github/workflows/ci.yml`: runs `npm ci`, `lint` and `build` on pushes to `main` and on pull requests.
- `.github/workflows/deploy.yml`: on every push to `main` (or a manual run), builds with `--base=/<repo-name>/` and deploys `dist/` to GitHub Pages. The base path is set only in CI, so `vite.config.ts` has no `base`. Local dev serves from `/`.

## Architecture & conventions

- Entry chain: `index.html` → `src/main.tsx` (mounts `<App />` in `StrictMode` on `#root`) → `src/App.tsx`.
- TypeScript uses project references. `tsconfig.json` points to `tsconfig.app.json`, which covers `src/` with browser and Vite client types. It also points to `tsconfig.node.json`, which covers only `vite.config.ts` with Node types. Both are `noEmit`, so Vite does the bundling.
- These compiler flags affect how you write code:
  - `verbatimModuleSyntax`: type-only imports must use `import type`.
  - `erasableSyntaxOnly`: no `enum`, `namespace`, or constructor parameter properties. Use union types or `as const` objects instead.
  - `noUnusedLocals` / `noUnusedParameters`: unused code fails the build.
  - `allowImportingTsExtensions`: imports may include `.ts`/`.tsx` extensions.
- ESLint applies `react-hooks` and `react-refresh` (Vite preset). Files that export components should export only components, or HMR breaks.
- Components are named exports written as arrow functions (`export const App = () => ...`), not default exports.
- Static assets that are served as-is go in `public/` (referenced as `/file`). Imported assets go in `src/assets/`.

## Known gaps in the scaffold

- `src/main.tsx` imports `./index.css`, but that file does not exist yet, so `vite build` and the dev server will fail until it is created.
- `index.html` references `/favicon.svg`, but `public/` is empty.
