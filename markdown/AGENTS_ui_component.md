# AGENTS.md – UIXBase (Universal UI Component Library)

## 🧭 Project Overview
**Project Name:** UIXBase  
**Goal:** Build a web & mobile compatible, responsive UI component sharing platform. Users can preview live demos, copy source code (HTML/CSS/JS), and reuse it directly. CSS or theme replacement should instantly restyle components without breaking layout.

**Core Concept:**
> A unified design system platform providing components, templates, and plugins that adapt visually through CSS themes, inspired by Tailwind, Shadcn, and Material.

---

## 🧩 Product Requirements (PRD)

### 1. Product Vision
"Build once, preview everywhere — share, remix, and apply."

- Provide rich visual previews of components in various design styles.
- Support multiple CSS frameworks: Tailwind, Shadcn, Material, Bootstrap.
- Allow full theming flexibility by swapping CSS or variables.
- Extend beyond components — include templates and plugin patterns.

### 2. Core Features
| Feature | Description |
|----------|-------------|
| Component Preview | Live-rendered component demos for browser preview |
| Code Copy | Copy HTML / JS / CSS / React / Vue snippets |
| Style Switcher | Switch between Tailwind, Vanilla CSS, Material themes |
| Responsive Preview | Desktop / tablet / mobile preview tabs |
| Category System | Buttons, Forms, Layouts, Modals, Charts, Icons, etc. |
| Template & Plugin Support | Layout Templates, UI Plugins as reusable modules |
| Theme Override | CSS variable–based theme system, no recompile required |
| Docs & Playground | MDX-based usage docs and editable sandbox |
| AI Assistance | Suggests related components and cross-framework equivalents |
| Export & Embed | iframe embed or zip export for projects |

### 3. Target Users
- Frontend Developers (React, Vue, Next.js, etc.)  
- UI/UX Designers needing design–dev handoff consistency  
- AI Agents referencing frontend structures for code generation  
- Developers seeking ready-to-use responsive UI kits

### 4. User Flow
1. User selects a component category  
2. Preview component with responsive tabs  
3. Switch theme (Tailwind / Material / Vanilla CSS)  
4. Copy code → paste into HTML/CSS/JS environment  
5. Export template if needed (zip/CDN/embed)

### 5. Product Differentiation
- Framework-agnostic structure  
- CSS-swap-only theming architecture  
- Richer stylistic coverage than Tailwind UI / Shadcn  
- Fully responsive by default (flex + grid + Tailwind breakpoints)

### 6. Success Metrics
| Metric | Target |
|---------|---------|
| Monthly Active Users | ≥ 10,000 |
| Avg. Session Time | ≥ 3 min |
| Code Copy Rate | ≥ 30% |
| New Components / Month | ≥ 50 |
| CSS Swap Error Rate | ≤ 2% |

---

## ⚙️ Technical Requirements (TRD)

### 1. Architecture Overview
```
Frontend: Next.js + Tailwind + Shadcn + MDX (for documentation)
Backend: Supabase (auth & storage) or Firebase
API Layer: REST + GraphQL hybrid
Build: Vite / Turbopack
Rendering: SSR + CSR hybrid (React Server Components)
```

### 2. Component Structure
```
component/
 ├── core/ (logic)
 ├── style/ (CSS, Tailwind, theme vars)
 └── preview/ (demo UI + code blocks)
```

- Use flex/grid for responsive layout
- All styling controlled by CSS variables (`--color-primary`, `--font-base`)
- Props managed via JSON Schema
- Monaco Editor for live code preview

### 3. Tech Stack
| Layer | Tech |
|-------|------|
| UI Framework | Next.js 15, React 19 |
| Styling | TailwindCSS + Shadcn + CSS Variables |
| Docs | MDX + Contentlayer |
| State | Zustand / Jotai |
| Code Preview | Monaco Editor |
| Build | Turborepo / Vite |
| Auth & Storage | Supabase / Clerk |
| Deployment | Vercel (frontend), Cloudflare R2 (assets) |

### 4. Responsive Design
- Supports desktop, tablet, and mobile breakpoints:
```css
@media (max-width: 640px) { /* mobile */ }
@media (min-width: 641px) and (max-width: 1024px) { /* tablet */ }
@media (min-width: 1025px) { /* desktop */ }
```
- PWA-enabled (offline mode, installable)

### 5. Theming System
```css
:root {
  --color-primary: #2563eb;
  --color-bg: #fff;
  --radius-md: 8px;
}
[data-theme="dark"] {
  --color-bg: #0f172a;
}
```
- Swappable color variables and font scales
- Compatible with Tailwind’s theme extension

### 6. Template & Plugin Framework
- **Template**: layout collections (Dashboard, Blog, etc.)  
- **Plugin**: logic modules (Toast, Modal, DatePicker)  
- `manifest.json` metadata for recognition:
```json
{
  "type": "plugin",
  "name": "toast",
  "frameworks": ["vanilla", "react"],
  "themes": ["light", "dark"]
}
```

### 7. AI Integration
- Component similarity recommendation (OpenAI Embeddings)
- Cross-framework code transformation (Tailwind ↔ Chakra ↔ CSS Modules)
- AI-assisted style generation

### 8. Testing & QA
| Type | Tool |
|------|------|
| Unit Test | Vitest / Jest |
| E2E Test | Playwright |
| Visual Regression | Percy |
| Linting | ESLint + Prettier |

### 9. Deployment
- GitHub Actions for CI/CD  
- Vercel → Preview → Main → Production branches  
- Version tagging: `uixbase-v1.0.0`  
- Cloudflare CDN caching

### 10. Extensibility
- Shadow DOM isolation (planned)
- Plugin Registry (community submissions)
- Design Token Export: `.json`, `.scss`, `.css`

---

## ✅ Summary
UIXBase merges the practicality of **Shadcn + Tailwind** with the live preview experience of **CodePen**, forming a unified, responsive, and theme-flexible UI library hub.  
It provides **template-, plugin-, and component-level** flexibility for rapid frontend prototyping and consistent design system management.

