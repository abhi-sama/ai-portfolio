# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview
- Next.js App Router project (using the `app` directory) bootstrapped with `create-next-app`.
- TypeScript + React 19 with Tailwind CSS 4 for styling.
- Biome is used for linting and formatting, with Next.js and React recommended rules enabled.

## Commands

### Local development
- Start the development server (npm is the default here because `package-lock.json` is checked in):

```bash
npm run dev
```

The app will be available at `http://localhost:3000`.

### Production build & run
- Build a production bundle:

```bash
npm run build
```

- Start the production server (after a successful build):

```bash
npm run start
```

### Linting & formatting
- Run Biome checks (lint + basic validation) over the project:

```bash
npm run lint
```

- Format supported files in-place using Biome:

```bash
npm run format
```

Biome respects VCS settings and ignores `node_modules`, `.next`, `dist`, and `build` (see `biome.json`).

### Tests
- There is currently no test runner or `test` script configured in `package.json`.
- Before expecting `npm test` or single-test workflows to work, a test framework (e.g. Vitest, Jest, Playwright) must be added and wired up in `package.json`.

## Code Layout & Architecture

### App Router structure
- `app/layout.tsx`
  - Defines `RootLayout`, which wraps all pages with the global `<html>`/`<body>` shell.
  - Loads Geist and Geist Mono via `next/font/google`, exposes them as CSS variables, and applies them on the `<body>` element along with `antialiased`.
  - Exports `metadata` (title/description) used by Next.js for document metadata.
  - Imports global styles from `app/globals.css`.

- `app/page.tsx`
  - Implements the home page UI.
  - Uses `next/image` and SVG assets from `public/` to render the logo and "Deploy"/"Documentation" calls to action.
  - Layout is implemented with Tailwind utility classes, including responsive behavior and dark-mode styles.

- `public/`
  - Contains static SVG assets referenced by `app/page.tsx` (e.g. `next.svg`, `vercel.svg`, plus additional icons you can reuse across future pages).

### Styling
- Global styles live in `app/globals.css` and are applied via `RootLayout`.
- Tailwind CSS 4 is wired through PostCSS (`postcss.config.mjs` uses the `@tailwindcss/postcss` plugin) and is activated via `@import "tailwindcss";` at the top of `globals.css`.
- Theme tokens are defined using Tailwind v4’s `@theme inline` API, mapping to CSS custom properties:
  - `--color-background` / `--color-foreground` derive from `--background` / `--foreground`.
  - Font tokens reference the Geist font CSS variables.
- Dark mode is handled via a `prefers-color-scheme: dark` media query that flips the `--background` and `--foreground` colors.

### Tooling & configuration
- `biome.json`
  - Enables Biome with Git VCS integration and `useIgnoreFile: true`.
  - Treats unknown files as ignored and excludes common build artifacts via `files.includes`.
  - Enables formatter with 2-space indentation.
  - Enables recommended lint rules globally, plus Next.js/React domain-specific recommended rules.
  - Configures `assist.actions.source.organizeImports` to run automatically when supported.

- `tsconfig.json`
  - Strict TypeScript configuration (`strict: true`, `noEmit: true`).
  - Uses `moduleResolution: "bundler"` and a `next` TypeScript plugin, matching Next.js  app-router best practices.
  - Defines a path alias `@/*` pointing at the project root, so imports like `@/app/page` are valid across the codebase.

- `next.config.ts`
  - Currently uses the default `NextConfig` with no custom options; add to this file if you need custom routing, image domains, headers, etc.

### Framework documentation
- For framework-level behavior (routing, data fetching, etc.), follow the official Next.js documentation linked from `README.md` (Next.js docs, Learn Next.js, and deployment docs for Vercel).