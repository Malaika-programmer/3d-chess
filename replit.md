# 3D Chess with Dice Turn Mechanic

## Overview

This is a 3D chess game with a custom dice-based turn mechanic. The core game logic lives in `client/index.html` as a self-contained implementation using Three.js (via CDN) for 3D rendering and Tailwind CSS for 2D UI elements. The React/Express scaffolding exists as a shell but is not yet wired into the game — the actual chess game is in the standalone HTML file.

**Core game concept:**
- Two players (White and Black) play chess on a 3D board
- Before the game starts, players roll a d6 to determine who goes first (first to roll a "1" wins)
- During play, after each move, the active player rolls again — rolling a "1" lets them move again; any other result passes the turn

The project brief is included in `attached_assets/` and describes the full intended feature set. The React app shell (`client/src/`) is mostly scaffolding with placeholder/commented-out code and is not yet used for the chess game.

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Frontend Architecture

**Two parallel frontend implementations exist:**

1. **Standalone HTML game** (`client/index.html`): The actual chess game. Uses:
   - Three.js r128 via CDN for 3D rendering and OrbitControls
   - Tailwind CSS via CDN for 2D UI panels (dice roller, status panel)
   - Vanilla JavaScript for all game logic
   - Optional Firebase integration (commented out, currently runs in demo/local mode)

2. **React app shell** (`client/src/`): A Vite + React + TypeScript setup that is mostly unused/placeholder. Key files:
   - `App.tsx` — renders an empty div; all game logic is commented out
   - `lib/stores/useGame.tsx` — Zustand store for game phase (`ready` / `playing` / `ended`)
   - `lib/stores/useAudio.tsx` — Zustand store for audio management
   - `components/ui/` — Full shadcn/ui component library (Radix UI primitives + Tailwind)
   - `lib/queryClient.ts` — TanStack Query setup for API calls

**3D Rendering approach:**
- Three.js is used directly (CDN version in the standalone HTML)
- The React app has `@react-three/fiber`, `@react-three/drei`, and `@react-three/postprocessing` installed for potential future React-based 3D
- `vite-plugin-glsl` is configured for GLSL shader support
- Assets include support for `.gltf`, `.glb`, `.mp3`, `.ogg`, `.wav` files

### Backend Architecture

- **Express.js** server (`server/index.ts`) with TypeScript
- Routes registered via `server/routes.ts` — currently empty (no API routes defined)
- In development: Vite middleware is integrated into Express for HMR
- In production: serves pre-built static files from `dist/public`
- Storage layer (`server/storage.ts`): `MemStorage` class implementing `IStorage` — currently in-memory only, no DB queries wired up despite Drizzle/Postgres being configured

### Data Storage

- **Drizzle ORM** with **PostgreSQL** (via `@neondatabase/serverless`)
- Schema defined in `shared/schema.ts`:
  - `users` table: `id` (serial PK), `username` (unique text), `password` (text)
  - Zod validation schemas auto-generated via `drizzle-zod`
- Database URL required via `DATABASE_URL` environment variable
- Migrations output to `./migrations/`
- **Current state**: Schema exists but the storage layer uses in-memory storage, not the DB

### Path Aliases

- `@/*` → `client/src/*`
- `@shared/*` → `shared/*`

### Multiplayer (Optional/Stubbed)

The standalone HTML has Firebase Firestore integration stubbed out:
- Firebase SDK import is commented out
- `window.firestoreDB` is set to `null` (demo/local mode)
- To enable: uncomment the Firebase module block and add real Firebase config credentials

## External Dependencies

### Core Framework
- **React 18** + **TypeScript** — frontend framework
- **Vite** — build tool and dev server
- **Express.js** — backend HTTP server
- **tsx** — TypeScript execution for the dev server

### 3D Graphics
- **Three.js** (CDN r128) — used in the standalone HTML chess game
- **@react-three/fiber** — React renderer for Three.js (installed, not yet used)
- **@react-three/drei** — Three.js helpers for React (installed, not yet used)
- **@react-three/postprocessing** — Post-processing effects (installed, not yet used)
- **vite-plugin-glsl** — GLSL shader file support in Vite

### UI Components
- **shadcn/ui** — Full component library built on Radix UI primitives
- **Radix UI** — Accessible headless UI primitives (full suite installed)
- **Tailwind CSS** — Utility-first CSS framework
- **Lucide React** — Icon library
- **class-variance-authority**, **clsx**, **tailwind-merge** — Styling utilities

### State Management & Data Fetching
- **Zustand** — Client-side state management (game phase, audio)
- **TanStack React Query** — Server state management and API caching

### Database
- **Drizzle ORM** — Type-safe ORM
- **@neondatabase/serverless** — Neon PostgreSQL serverless driver
- **drizzle-zod** — Auto-generate Zod schemas from Drizzle table definitions
- **Zod** — Runtime schema validation

### Session
- **connect-pg-simple** — PostgreSQL-backed Express session store (installed, not yet wired up)

### Fonts
- **@fontsource/inter** — Inter font (self-hosted)

### Optional / Stubbed
- **Firebase** (Firestore) — Commented-out multiplayer backend in `client/index.html`