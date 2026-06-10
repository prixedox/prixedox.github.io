# Upcoming projects — plan

Three new pages for prixedox.github.io, in build order. Each follows the
existing pattern: **own repo → GitHub Pages → card on the landing page**,
dark theme matching the site, and (where there's real logic) a self-test
page verified in headless Chrome before deploying.

---

## 1. Bio-Inspired Algorithms Playground — repo `bio-playground`

**URL:** `https://prixedox.github.io/bio-playground/`
**Stack:** vanilla JS + canvas, zero dependencies (same as nn-playground)

One page, four algorithm tabs. Each tab = live canvas animation + parameter
sliders + a Run/Pause/Reset toolbar, so you can *watch* the algorithm search.

| Tab | Algorithm | Visualization |
|-----|-----------|---------------|
| **Evolution** | Genetic algorithm on the Travelling Salesman Problem | Cities + best tour so far, fitness-over-generations chart, population diversity |
| **Ants** | Ant Colony Optimization, also on TSP — directly comparable with the GA | Pheromone trails drawn with opacity = strength, ants walking the graph |
| **Swarm** | Particle Swarm Optimization minimising 2-D test functions (Rastrigin, Himmelblau, Ackley...) | Function landscape as a heatmap, particles with velocity trails converging on minima |
| **Flock** | Boids (Reynolds rules: separation / alignment / cohesion) | Classic flocking animation; sliders for each rule weight, optional predator chasing the flock |

**Parameters exposed:** population size, mutation & crossover rate, elitism
(GA); ant count, pheromone evaporation, α/β weights (ACO); inertia,
cognitive/social coefficients (PSO); rule weights, vision radius, speed (Boids).

**Why these four:** they cover the main families — evolutionary, stigmergy,
swarm intelligence, emergent behaviour — and TSP appearing twice lets you
race GA vs. ACO on the same city layout.

**Self-tests:** GA improves tour length on a known instance; ACO beats random
tours; PSO finds Rastrigin's global minimum basin; boids rules are
direction-correct on hand-built configurations.

---

## 2. Ray Tracer — repo `ray-tracer`

**URL:** `https://prixedox.github.io/ray-tracer/`
**Stack:** vanilla JS + canvas + **Web Workers** (one per CPU core), zero dependencies

A real ray tracer written from scratch — not WebGL shader tricks. The image
renders **progressively**: a fast preview pass first, then samples accumulate
and the noise melts away while you watch.

**Rendering features:**
- Sphere / plane / box intersections, hand-written vector math
- Materials: diffuse (Lambert), metal with adjustable roughness, glass with
  refraction + Fresnel (Schlick), emissive lights, checkerboard texture
- Soft shadows from area lights, reflections with configurable bounce depth,
  anti-aliasing via jittered multi-sampling, gamma correction
- Tiled rendering split across Web Workers so all CPU cores work in parallel

**UI:** scene presets (Cornell-box-style room, sphere garden, glass showcase,
mirror corridor), orbit/zoom camera controls (re-render on release), sliders
for bounce depth / samples / resolution, live rays-per-second + progress
counter, "Save PNG" button.

**Self-tests:** ray–sphere/plane/box intersection cases with known answers,
refraction directions against Snell's law, energy sanity (no pixel brighter
than its light source), deterministic 64×64 scene render checksum.

---

## 3. Vzdělej.se → GitHub Pages — existing repo `vzdelej-se`

**URL:** `https://prixedox.github.io/vzdelej-se/`

The repo is already a perfect candidate: Next.js 16 app that is **client-side
only** (static TS content, no database, no auth, no server calls). GitHub
Pages can only serve static files, so the work is configuration, not rewriting:

1. **Static export** — in `next.config.ts`: `output: 'export'`,
   `basePath: '/vzdelej-se'`, `images: { unoptimized: true }`,
   `trailingSlash: true` (Pages serves folders, not extensionless files).
2. **Audit for server-only features** — any dynamic route needs
   `generateStaticParams` (lesson/topic pages are static TS objects, so this
   should be mechanical); no API routes / server actions / `headers()` may
   remain. Fix whatever the export build flags.
3. **Root-relative URLs** — internal links/assets must respect `basePath`
   (Next handles `<Link>` and `next/image` automatically; hardcoded `/...`
   paths in CSS or content need a sweep).
4. **CI deploy** — GitHub Actions workflow: pnpm install → `next build`
   (export) → upload `out/` to Pages. Every push to `main` redeploys
   automatically — no manual builds.
5. **Verify** — run the exported site locally, click through lessons (KaTeX,
   drag-to-sort, interactive visuals), then check the live URL.
6. **Landing page** — add the card: *"Vzdělej.se — Czech interactive math &
   physics lessons, Brilliant-style."*

**Risk note:** this is the only project with real unknowns — 35 lessons and
31 interactive components may hide a few export-incompatible spots
(e.g. `useSearchParams` without Suspense). The export build will list them;
expect small mechanical fixes rather than redesign. If the site should later
live at the actual `vzdelej.se` domain, that's a one-line CNAME change on
top of this setup.

---

## Order & status

| # | Project | Status |
|---|---------|--------|
| 1 | bio-playground | **live** |
| 2 | ray-tracer | **live** |
| 3 | vzdelej-se on Pages | planned |

Each goes live (repo + Pages + landing card) before the next one starts.
