# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

This is a Turborepo + pnpm monorepo.

```bash
# Root commands (run from project root)
pnpm install          # Install all dependencies
pnpm build            # Build all packages
pnpm dev              # Run dev server for all apps
pnpm dev:web          # Run dev server for web app only (http://localhost:5173)
pnpm test             # Run tests in watch mode
pnpm test:run         # Run tests once

# Package-specific commands (from package directory)
cd packages/core && pnpm build       # Build core package
cd packages/core && pnpm test:run    # Run core tests
cd apps/web && pnpm dev              # Run web dev server
```

## Monorepo Structure

```
rsvp-reading/
├── apps/
│   ├── web/                    # @rsvp/web - Svelte 5 web application
│   │   ├── src/
│   │   │   ├── lib/components/ # Svelte UI components
│   │   │   ├── App.svelte      # Main orchestrator
│   │   │   ├── app.css
│   │   │   └── main.js
│   │   ├── index.html
│   │   ├── package.json
│   │   └── vite.config.js
│   └── desktop/                # @rsvp/desktop - Electron (placeholder)
│       └── package.json
├── packages/
│   └── core/                   # @rsvp/core - Shared utilities (TypeScript)
│       ├── src/
│       │   ├── index.ts
│       │   ├── rsvp-utils.ts
│       │   ├── file-parsers.ts
│       │   └── progress-storage.ts
│       ├── tests/
│       └── package.json
├── pnpm-workspace.yaml
├── turbo.json
├── package.json
└── tsconfig.base.json
```

## Architecture Overview

This is a Svelte 5 speed-reading application using RSVP (Rapid Serial Visual Presentation) to display text one word at a time. It supports PDF and EPUB file uploads.

### Core Concept: Optimal Recognition Point (ORP)

The app highlights a "focal letter" in red based on word length - this is the ORP where the eye naturally focuses:
- 1-3 letters: 1st letter
- 4-5 letters: 2nd letter
- 6-9 letters: 3rd letter
- 10+ letters: 4th letter

### Package: @rsvp/core

Shared TypeScript utilities, platform-agnostic:
- `rsvp-utils.ts` - ORP calculations, word timing, text parsing
- `file-parsers.ts` - PDF extraction (pdfjs-dist) and EPUB extraction (epubjs)
- `progress-storage.ts` - localStorage session persistence

### App: @rsvp/web

Svelte 5 web application:
- `App.svelte` - Main orchestrator, contains all application state
- `lib/components/RSVPDisplay.svelte` - Large monospace word display with ORP highlighting
- `lib/components/Controls.svelte` - Play/Pause/Stop buttons (adapts to focus mode)
- `lib/components/Settings.svelte` - WPM, fade effects, punctuation pauses configuration
- `lib/components/TextInput.svelte` - File upload and text paste panel
- `lib/components/ProgressBar.svelte` - Clickable progress bar with seek

### Key Patterns

- **Focus mode**: During playback, UI minimizes to reduce distractions
- **Keyboard-first**: Space (play/pause), arrows (speed/navigation), G (jump), Ctrl+S (save)
- **PDF worker**: Loaded from unpkg CDN, not bundled locally
- **Dark theme**: Black background with red (#ff4444) ORP highlights
- **Shared code**: Core utilities imported via `@rsvp/core` workspace package

### Test Files

Tests are in `packages/core/tests/` covering the three utility modules. Uses Vitest with jsdom environment.

```bash
# Run all tests
pnpm test:run

# Run single test file
cd packages/core && npx vitest run tests/rsvp-utils.test.ts
```
