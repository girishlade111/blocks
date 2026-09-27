# V0 Blocks — 3D Brick Builder

An interactive **3D block-building playground** (LEGO-style) in the browser.
Place, move, rotate, and erase colorful bricks on a 3D plane, render them in
real time with Three.js, and save / load your creations — all wrapped in a
sleek dark v0-style UI with full keyboard-shortcut and touch support.

## What it does

- Build structures brick-by-brick on an interactive 3D canvas (React Three
  Fiber + drei `OrbitControls` for orbit / zoom / pan).
- Pick brick colors from curated palettes and color themes (custom
  `useColorTheme` hook).
- Build / Move / Erase interaction modes with a ghost preview of the brick
  being placed.
- Resizable bricks: adjust width / height / depth of the selected brick.
- Full history: undo / redo stacks, clear-with-confirmation, and
  keyboard shortcuts (`useKeyboardShortcuts`).
- Save creations (named, with thumbnails) and load them back later:
  - Cloud persistence via **Vercel KV** server actions when
    `KV_REST_API_URL` / `KV_REST_API_TOKEN` are configured.
  - Local browser `localStorage` fallback (autosave) when KV is not
    configured.
- Background music / audio player with play-pause toggle.
- Responsive: desktop toolbars collapse into a mobile toolbar with touch
  handling (`useTouchHandling`), plus tooltips, popovers, dialogs.

## Tech stack

- **Framework:** Next.js 14 (App Router) + React 18 + TypeScript
- **3D:** `@react-three/fiber`, `@react-three/drei`, `three`
- **UI:** Tailwind CSS, shadcn-style components, Radix UI primitives,
  `lucide-react` icons, `sonner` toasts
- **State/storage:** React hooks, Vercel KV (`@vercel/kv`), browser
  `localStorage`, `nanoid`
- **Forms/validation:** `react-hook-form`, `zod`, `@hookform/resolvers`
- **Charts/misc:** `recharts`, `embla-carousel-react`, `cmdk`,
  `react-resizable-panels`, `vaul`
- **Tooling:** ESLint, PostCSS, pnpm

## Quick start

```bash
pnpm install
pnpm dev        # http://localhost:3000
```

Build for production:

```bash
pnpm build
pnpm start
```

Type-check / lint:

```bash
pnpm lint
```

> Note: this project uses pnpm. If `pnpm install` errors with
> `EPERM ... chown` (containers without `CAP_CHOWN`), the install still
> links packages into `node_modules`; only lockfile writes fail.

## Environment variables (optional)

Cloud save/load works out of the box in the browser via `localStorage`.
For shared cloud persistence, connect Vercel KV and set:

| Variable            | Description                      |
| ------------------- | -------------------------------- |
| `KV_REST_API_URL`   | Vercel KV REST endpoint          |
| `KV_REST_API_TOKEN` | Vercel KV REST token             |

Without these, the app shows an integration notice and falls back to local
storage automatically.

## Project structure

```
app/                  # Next.js App Router (layout, page, globals.css)
components/
  v0-blocks/          # Main builder: events, hooks (shortcuts, theme, storage, touch)
  scene/              # 3D scene, build/erase modes, lighting, interaction
  block/              # Brick mesh component + types
  color-selector/     # Palette picker, dimension/history/file controls
  ui/                 # shadcn-style primitives (button, dialog, popover, dropdown)
  action-toolbar.tsx  # Top toolbar (build/move/erase, undo/redo, play)
  save-modal.tsx      # Save creation dialog (KV server action)
  load-modal.tsx      # Load creations dialog (KV server action)
lib/
  actions/            # "use server" actions: save/get/update/delete creations (Vercel KV)
  utils/              # local-storage helpers, KV integration check, color utils
  constants.ts        # Brick defaults, palettes, themes
public/               # Static assets, audio
next.config.mjs       # Next.js config (images unoptimized for static hosts)
```

## Deployment

Standard Next.js hosting works (Vercel, Netlify, or any Node host):

```bash
pnpm build && pnpm start
```

Because the app uses Next.js **server actions** (`"use server"` in
`lib/actions/*` for Vercel KV cloud saves), it cannot be deployed as a
pure static export — a server runtime is required for the cloud
save/load features (local mode works anywhere).

---

Built by Girish Lade — https://ladestack.in
