# Lucas Kara — Personal Google Search Portfolio

**Roadmap Version:** 1.0.0  
**Last Updated:** 2026-05-25  
**Current Position:** Phase 1: ES6 Module Foundation & Component Extraction  
**Status:** `█████░░░░░░░░░░░░░░ 25% (Structured Planning → Active Development)`

---

## Project Philosophy

This portfolio is a **semantic search interface** that surfaces you — your abilities, projects, career, and qualifications — exactly as if Google indexed *you* as discoverable content. The goal is to maintain that **pristine, intentional HTML structure** while transitioning the JavaScript from inline scripts to a clean, maintainable ES6 module architecture.

**Core Design Principle:** Creative HTML flexibility + ES6 modularity = No framework overhead, maximum developer velocity, and browser-friendly inspection.

---

## Roadmap Structure

This roadmap follows a **3-Phase progression**, each with exactly 6 tasks. Each phase represents a complete, shippable milestone toward a fully modernized, maintainable codebase.

```
Phase 1: ES6 Module Foundation & Component Extraction (Current)
    ↓
Phase 2: Semantic Content Architecture & Search Integration
    ↓
Phase 3: Advanced Portfolio Features & Performance Optimization
```

---

## Phase 1: ES6 Module Foundation & Component Extraction

> **Goal:** Transform inline `<script>` blocks into isolated ES6 modules. Extract theme toggle, modal system, and app drawer into reusable components. Establish the module architecture pattern and build tooling that will scale across all portfolio pages.

---

### Task 01: ES6 Module Architecture & Build Setup

**Description:**
Set up `package.json` with a lightweight build pipeline using `esbuild` or `tsup`. Create a `modules/` directory structure with:
- `modules/index.ts` — main entry point
- `modules/theme/` — dark mode utilities
- `modules/modal/` — modal manager
- `modules/ui/` — shared UI helpers (tooltips, animations)
- `modules/navigation/` — app drawer & navigation logic

Configure a `scripts.build` task that bundles all modules into a single `dist/bundle.js` and a `scripts.dev` task that rebuilds on file changes. Update `index.html`, `career.html`, `projects.html`, and `qualifications.html` to load `<script type="module" src="dist/bundle.js"></script>` instead of inline `<script>` tags.

**Acceptance Criteria:**
- [ ] `package.json` exists with `build`, `dev`, and `lint` scripts
- [ ] `modules/` directory created with TypeScript configuration
- [ ] Running `npm run build` generates `dist/bundle.js` without errors
- [ ] Running `npm run dev` watches for changes and rebuilds automatically
- [ ] All four HTML files load the bundled script successfully
- [ ] Page functionality is identical to current behavior (no regressions)
- [ ] Browser console shows no errors or warnings on page load

**Definition of Done:**
- Git commit with message: `feat(build): establish ES6 module architecture and esbuild pipeline`

---

### Task 02: Dark Mode Module Extraction

**Description:**
Extract the dark mode toggle logic from inline scripts in `index.html`, `career.html`, `projects.html`, and `qualifications.html` into a reusable `modules/theme/DarkMode.ts` module.

The module should export:
```typescript
export class DarkMode {
  constructor()
  toggle(): void
  setDarkMode(isDark: boolean): void
  isDarkMode(): boolean
  onThemeChange(callback: (isDark: boolean) => void): void
}
```

Use `localStorage` to persist the user's theme preference across page reloads. The module should query and update the DOM (`body.classList.toggle('dark-mode')`) and sync the theme icon (`#theme-icon`) and tooltip (`#theme-tooltip`) automatically.

Extract the CSS variables (`:root` and `body.dark-mode` selectors) into a shared `styles/theme.css` file and import it in each HTML file's `<head>`.

**Acceptance Criteria:**
- [ ] `modules/theme/DarkMode.ts` exports a working `DarkMode` class
- [ ] Clicking the theme toggle button switches themes instantly
- [ ] Theme preference persists across page reloads via `localStorage`
- [ ] Theme icon and tooltip text update correctly
- [ ] All CSS variables (shadows, colors, borders) apply correctly in both light and dark modes
- [ ] No inline `<script>` tags remain in HTML files for theme logic
- [ ] Running `npm run lint` passes without errors

**Definition of Done:**
- Git commit with message: `refactor(theme): extract dark mode to ES6 module`
- Single `styles/theme.css` file imported by all HTML pages

---

### Task 03: Modal System Module Extraction

**Description:**
Extract the modal management logic from inline scripts into a `modules/modal/ModalManager.ts` module.

The module should export:
```typescript
export class ModalManager {
  constructor()
  open(key: string, title: string, items: string[]): void
  close(): void
  setData(key: string, data: ModalData): void
  getData(key: string): ModalData | null
}

interface ModalData {
  title: string
  items: string[]
}
```

The manager should automatically handle:
- Opening/closing the `.modal-overlay` and `.modal` elements
- Populating `#modal-title` and `#modal-list` with content
- Handling both keyboard (Escape) and click-outside dismissal
- Smooth animations (fade-in, slide-in) using CSS transitions

Initialize the manager on page load and expose it globally (for backward compatibility during transition) as `window.__portfolioModal`.

**Acceptance Criteria:**
- [ ] `modules/modal/ModalManager.ts` exports a working `ModalManager` class
- [ ] `openModal()` calls in HTML are replaced with `ModalManager.open()` calls
- [ ] Modals open and close smoothly with animations
- [ ] Closing works via: (a) close button, (b) Escape key, (c) clicking outside
- [ ] Modal content is pre-populated correctly from the `modalData` object
- [ ] No regression in modal behavior from current implementation
- [ ] TypeScript compilation passes without errors

**Definition of Done:**
- Git commit with message: `refactor(modal): extract modal management to ES6 module`
- All HTML files reference `ModalManager` instead of inline `openModal()` calls

---

### Task 04: Apps Drawer & Navigation Module Extraction

**Description:**
Extract the app grid rendering and drawer toggle logic from inline scripts into a `modules/navigation/AppsDrawer.ts` module.

The module should export:
```typescript
export class AppsDrawer {
  constructor(config: AppsDrawerConfig)
  render(container: HTMLElement): void
  toggle(): void
  addApp(app: PortfolioApp): void
  removeApp(name: string): void
  onAppClick(callback: (app: PortfolioApp) => void): void
}

interface PortfolioApp {
  name: string
  icon: string
  url: string
  color: string
  isFab?: boolean
}
```

The module should:
- Accept a configuration object (list of apps) in the constructor
- Render the app grid dynamically in `#apps-grid`
- Handle open/close toggle with click-outside detection
- Support adding/removing apps dynamically (for future extensibility)
- Generate correct markup with correct FontAwesome icon classes (`fas` vs `fab`)

Extract the `portfolioApps` array into a `config/apps.ts` file that the drawer imports.

**Acceptance Criteria:**
- [ ] `modules/navigation/AppsDrawer.ts` exports a working `AppsDrawer` class
- [ ] `renderApps()` logic is now inside the module
- [ ] Apps drawer opens and closes correctly
- [ ] Click-outside detection works (drawer closes when clicking outside)
- [ ] All app links open correctly (external links open in new tab)
- [ ] App colors and icons render correctly
- [ ] No regression in drawer behavior
- [ ] The drawer initialization call in each HTML file is a single line

**Definition of Done:**
- Git commit with message: `refactor(navigation): extract apps drawer to ES6 module`
- New file: `config/apps.ts` containing the `portfolioApps` array

---

### Task 05: Shared UI Utilities & Animation Module

**Description:**
Extract animation helpers, tooltip logic, and shared DOM utilities into a `modules/ui/UIUtils.ts` module.

The module should export:
```typescript
export class UIUtils {
  static fadeInUp(element: HTMLElement, delay?: number): void
  static slideInFromRight(element: HTMLElement, delay?: number): void
  static tooltipManager: TooltipManager
  static ensureA11y(): void
}

export class TooltipManager {
  constructor()
  bindTooltips(container: HTMLElement): void
  show(el: HTMLElement): void
  hide(el: HTMLElement): void
}
```

The module should provide:
- Reusable CSS animation trigger functions (bind to scroll or visibility)
- Tooltip keyboard accessibility (arrow key navigation, focus management)
- Utility functions to add/remove CSS classes with transitions
- Event delegation helpers for dynamic content

This module should be a "utilities belt" that other modules import to standardize animations and UI interactions across all pages.

**Acceptance Criteria:**
- [ ] `modules/ui/UIUtils.ts` exports working animation and utility functions
- [ ] Animations on the page trigger correctly (fadeInUp on results)
- [ ] Tooltips show/hide with proper visibility state management
- [ ] Keyboard navigation works on all interactive elements (tooltips, modals, tabs)
- [ ] ARIA labels are correctly applied to all interactive elements
- [ ] No regression in animation timing or visual behavior
- [ ] Module can be imported and used independently by other modules

**Definition of Done:**
- Git commit with message: `refactor(ui): extract shared utilities and animations to module`
- Updated HTML files to use `UIUtils` for initialization

---

### Task 06: Phase 1 E2E Validation & Test Suite Setup

**Description:**
Set up a lightweight test suite in `tests/` directory using a simple assertion library (e.g., `node:assert` or `vitest`). Create `tests/phase-1.test.ts` with assertions covering the functionality of all Phase 1 modules.

Write assertions for:
1. **Dark Mode Module:** Toggle dark mode, verify localStorage persists, verify CSS classes apply
2. **Modal Manager:** Open modal with data, close modal, verify DOM updates
3. **Apps Drawer:** Render apps, toggle drawer, verify click-outside closes drawer
4. **UI Utils:** Verify animation functions trigger CSS transitions without errors
5. **No Regressions:** Load each HTML page and verify page renders without console errors
6. **Bundle Size:** Verify `dist/bundle.js` is < 25KB (gzipped)

Create a `package.json` script `test` that runs the test suite. The goal is to ensure all four portfolio pages function identically to before the refactor.

**Acceptance Criteria:**
- [ ] `tests/phase-1.test.ts` exists with 6+ assertions
- [ ] Running `npm test` passes all assertions
- [ ] Each HTML page loads without console errors
- [ ] Dark mode toggle works on all 4 pages
- [ ] Modals open/close on all pages where they appear
- [ ] Apps drawer works on all pages
- [ ] Bundle size is documented and < 25KB gzipped
- [ ] All four pages render identically to current behavior

**Definition of Done:**
- Git commit with message: `test(phase-1): add comprehensive E2E validation suite`
- Test output shows: ✅ Phase 1: ES6 Module Foundation (6/6 assertions passing)
- README updated with test running instructions

---

## Phase 2: Semantic Content Architecture & Search Integration

> **Goal:** Enhance the portfolio with semantic search capabilities. Implement client-side full-text search across all pages, add rich metadata to portfolio items, and build a unified search engine that surfaces content by meaning (not just keywords). This phase transforms the portfolio from a static document into a dynamic, discoverable knowledge base.

---

### Task 07: Semantic Metadata & Content Structure

**Description:**
Create a `data/portfolio.json` file that centralizes all portfolio content as structured, semantic JSON. The structure should look like:

```json
{
  "profile": { "name": "Lucas Kara", "title": "...", "bio": "..." },
  "career": [
    {
      "id": "eecol-role",
      "title": "Wire Cutter & Material Handler",
      "company": "EECOL Electric",
      "tags": ["operations", "automation", "teamwork"],
      "description": "...",
      "highlights": ["..."]
    }
  ],
  "projects": [
    {
      "id": "wire-tools-suite",
      "title": "EECOL Wire Tools Suite",
      "tags": ["pwa", "local-first", "industrial"],
      "description": "...",
      "repository": "https://github.com/..."
    }
  ],
  "qualifications": [
    {
      "id": "forklift-cert",
      "title": "Certified Forklift Operator",
      "tags": ["safety", "operations"],
      "issuer": "WorkSafeBC"
    }
  ]
}
```

Create a `modules/content/ContentLoader.ts` that imports this JSON and provides a unified API:

```typescript
export class ContentLoader {
  static async loadPortfolio(): Promise<Portfolio>
  static search(query: string): SearchResult[]
  static getCareerItems(): CareerItem[]
  static getProjects(): ProjectItem[]
  static getQualifications(): QualificationItem[]
}
```

Update each page (`career.html`, `projects.html`, `qualifications.html`) to use this loader instead of hardcoded HTML data.

**Acceptance Criteria:**
- [ ] `data/portfolio.json` contains all current portfolio content
- [ ] `modules/content/ContentLoader.ts` exports working methods
- [ ] Each page loads data from the loader and renders it dynamically
- [ ] All content is displayed identically to current HTML
- [ ] Search method returns relevant results by tag matching
- [ ] No regression in page rendering or functionality
- [ ] JSON structure is validated and documented

**Definition of Done:**
- Git commit with message: `refactor(content): extract portfolio data to semantic JSON and loader module`
- Updated HTML pages render content from JSON source of truth

---

### Task 08: Full-Text Search Module

**Description:**
Create a `modules/search/SearchEngine.ts` module that implements a lightweight, client-side full-text search across all portfolio content.

The module should export:
```typescript
export class SearchEngine {
  constructor(portfolio: Portfolio)
  search(query: string, options?: SearchOptions): SearchResult[]
  fuzzyMatch(query: string, text: string): number
  highlightMatches(text: string, query: string): string
}

interface SearchOptions {
  limit?: number
  fuzzy?: boolean
  fields?: string[]
}

interface SearchResult {
  id: string
  type: 'career' | 'project' | 'qualification'
  title: string
  relevance: number
  snippet: string
}
```

The search engine should:
- Support exact keyword matching
- Support fuzzy matching (typo tolerance)
- Rank results by relevance (exact matches first, then fuzzy)
- Return highlighted snippets showing where the match occurred
- Filter by type if specified
- Return results in < 50ms for typical queries

Implement the search using a simple inverted index or token-based matching (no heavy dependencies like Elasticsearch).

**Acceptance Criteria:**
- [ ] `modules/search/SearchEngine.ts` implements all required methods
- [ ] Searching "wire tools" returns projects containing "wire"
- [ ] Searching "forklift" returns qualifications with "forklift"
- [ ] Fuzzy search tolerates typos (e.g., "wirr tools" still matches "wire tools")
- [ ] Results are ranked by relevance
- [ ] Search completes in < 50ms
- [ ] Snippets are highlighted correctly with `<mark>` tags
- [ ] No external search dependencies (client-side only)

**Definition of Done:**
- Git commit with message: `feat(search): implement client-side full-text search engine`

---

### Task 09: Search UI & Global Search Bar Integration

**Description:**
Add a global search interface to the portfolio. Enhance the `.topbar-search` input on each page to use the `SearchEngine` instead of being static placeholder text.

Create a `modules/search/SearchUI.ts` module that:
- Listens to the `.topbar-search input` for debounced input (300ms debounce)
- Calls `SearchEngine.search()` on each keystroke
- Renders a dropdown results list below the search bar
- Shows results grouped by type (Career, Projects, Qualifications)
- Allows keyboard navigation (arrow keys, Enter to select)
- Navigates to the appropriate page and scrolls to the result on selection

Update `index.html` to be a search-focused landing page that displays:
1. The search bar prominently at the top
2. Quick-access cards for Career, Projects, and Qualifications
3. Recent/featured items from each category
4. Search results in a live-updating list as the user types

**Acceptance Criteria:**
- [ ] Typing in the search bar on any page updates results in < 300ms
- [ ] Results dropdown appears with correct grouping by type
- [ ] Clicking a result navigates to the appropriate page
- [ ] Keyboard navigation (arrow keys, Enter) works correctly
- [ ] Results display relevant snippets with highlighted matches
- [ ] No lag or stutter when typing (debouncing works)
- [ ] Search is accessible (ARIA labels, keyboard-only navigation)
- [ ] `index.html` is redesigned as a search-focused entry point

**Definition of Done:**
- Git commit with message: `feat(search-ui): implement global search bar and results interface`
- `index.html` serves as the search landing page

---

### Task 10: Dynamic Page Templates & Content Rendering

**Description:**
Create a page templating system that dynamically renders content from `portfolio.json` without duplication across HTML files.

Create `modules/render/PageRenderer.ts` that exports:
```typescript
export class PageRenderer {
  static renderCareerPage(container: HTMLElement): void
  static renderProjectsPage(container: HTMLElement): void
  static renderQualificationsPage(container: HTMLElement): void
}
```

Each page function should:
- Fetch portfolio data from `ContentLoader`
- Generate the appropriate modal dialogs dynamically
- Render the featured result, sitelinks grid, and "People Also Ask" section
- Wire up all event listeners (modal opens, accordion toggles, etc.)
- Apply animations

This allows `career.html`, `projects.html`, and `qualifications.html` to be minimal, with a single line in each: `<div id="app"></div>` and a script that calls `PageRenderer.renderCareerPage(document.getElementById('app'))`.

**Acceptance Criteria:**
- [ ] `modules/render/PageRenderer.ts` exports working page render functions
- [ ] Each page renders identically to current HTML implementation
- [ ] All modals, accordions, and interactions work correctly
- [ ] HTML file size is dramatically reduced (mostly shared boilerplate)
- [ ] Content updates in `portfolio.json` automatically reflect on pages
- [ ] No regression in page functionality or appearance
- [ ] Page renders in < 200ms (performance target)

**Definition of Done:**
- Git commit with message: `refactor(render): implement dynamic page templating system`
- Career, Projects, and Qualifications pages reduced to minimal HTML skeletons

---

### Task 11: Breadcrumb Navigation & Page Context

**Description:**
Implement a breadcrumb navigation system that shows the user's current location in the portfolio hierarchy.

Create `modules/navigation/Breadcrumbs.ts` that:
- Displays the current page (e.g., "Career" or "Projects")
- Shows the current category/section if applicable
- Updates dynamically as the user navigates
- Is clickable (clicking a breadcrumb navigates back to that section)

Update the `.topbar` on each page to include a breadcrumb component that shows:
- `Home > Career` (on career.html)
- `Home > Projects` (on projects.html)
- `Home > Qualifications` (on qualifications.html)

Add a small breadcrumb icon (e.g., `>`) between levels. Make it accessible with proper ARIA labels.

**Acceptance Criteria:**
- [ ] Breadcrumbs appear correctly on all pages
- [ ] Clicking a breadcrumb navigates to that section
- [ ] Breadcrumbs update when page content changes
- [ ] ARIA labels are present for screen readers
- [ ] Visual styling matches the portfolio design
- [ ] Breadcrumbs are responsive (collapse on small screens)

**Definition of Done:**
- Git commit with message: `feat(navigation): add breadcrumb navigation system`

---

### Task 12: Phase 2 Search & Content E2E Validation

**Description:**
Extend the test suite with `tests/phase-2.test.ts` covering all new search and content features.

Write assertions for:
1. **Content Loader:** Load portfolio.json, verify structure, retrieve career/project/qualification items
2. **Search Engine:** Search for "wire", verify results, test fuzzy matching, verify relevance ranking
3. **Search UI:** Type in search bar, verify dropdown appears, keyboard navigation works
4. **Page Templates:** Render each page dynamically, verify content matches static version
5. **Breadcrumbs:** Navigate between pages, verify breadcrumbs update correctly
6. **Performance:** Verify page render time < 200ms, search query time < 50ms

Run the full test suite (`npm test`) and verify all Phase 1 + Phase 2 assertions pass.

**Acceptance Criteria:**
- [ ] `tests/phase-2.test.ts` exists with 6+ assertions
- [ ] Running `npm test` passes all Phase 1 + Phase 2 assertions
- [ ] Search functionality works correctly across all test cases
- [ ] Page rendering performance meets targets
- [ ] No regression from Phase 1
- [ ] Test output clearly shows: ✅ Phase 2: Semantic Content Architecture (6/6 passing)

**Definition of Done:**
- Git commit with message: `test(phase-2): add comprehensive search and content validation`
- README updated with Phase 2 completion status
- All tests passing with clear output

---

## Phase 3: Advanced Portfolio Features & Performance Optimization

> **Goal:** Add advanced features that showcase your portfolio as a cutting-edge tool: lazy-loading images, service worker for offline support, analytics integration, and performance optimizations (code splitting, asset optimization). This phase transforms the portfolio from a well-organized codebase into a high-performance, modern web application.

---

### Task 13: Lazy Loading & Image Optimization

**Description:**
Implement lazy-loading for all images and heavy assets on the portfolio.

Create `modules/performance/LazyLoader.ts` that:
- Uses the Intersection Observer API to detect when images enter the viewport
- Loads images on-demand with a blur-up placeholder effect
- Supports responsive image sets (`srcset` attributes)
- Falls back gracefully for browsers without Intersection Observer support

Create an image optimization pipeline:
- Store original images in `assets/images/original/`
- Create a build script that generates optimized versions (WEBP, resized for mobile/tablet/desktop)
- Reference the optimized versions in HTML with `srcset` attributes

Add a `modules/performance/ImageOptimizer.ts` module that handles:
- LQIP (Low Quality Image Placeholder) generation
- Aspect ratio preservation to prevent layout shift
- Responsive image loading based on device pixel ratio

**Acceptance Criteria:**
- [ ] Images load lazily when they enter the viewport
- [ ] LQIP blur-up effect displays while loading
- [ ] Full resolution images load within 200ms
- [ ] No layout shift (CLS = 0)
- [ ] Responsive images load correct size for device
- [ ] Performance metrics improve (LCP, FID)
- [ ] Fallback works in older browsers

**Definition of Done:**
- Git commit with message: `perf(images): implement lazy loading and image optimization`
- Images load < 200ms on typical connection
- Lighthouse CLS score: 0.1 or better

---

### Task 14: Code Splitting & Dynamic Module Loading

**Description:**
Implement code splitting to reduce initial bundle size and improve time-to-interactive.

Create a module loading system that:
- Splits modules by page (e.g., `career.module.js`, `projects.module.js`)
- Lazy-loads page-specific modules only when navigating to that page
- Uses dynamic imports (`import()`) to load modules asynchronously
- Shows a loading indicator while modules load

Create `modules/loader/ModuleLoader.ts` that exports:
```typescript
export class ModuleLoader {
  static async loadPageModule(page: 'career' | 'projects' | 'qualifications'): Promise<void>
  static async loadComponent(component: string): Promise<any>
  static preloadModule(page: string): void
}
```

Update the HTML navigation to use `ModuleLoader` instead of eager loading all modules upfront. Implement smart preloading: when the user hovers over a navigation link, preload that page's module.

**Acceptance Criteria:**
- [ ] Initial bundle size < 15KB (core only)
- [ ] Page-specific modules load < 100ms
- [ ] Loading indicator displays while modules load
- [ ] No janky interactions during module loading
- [ ] Hover preloading works (modules load before click)
- [ ] Performance improves (Time to Interactive < 2s)
- [ ] Fallback works if module load fails

**Definition of Done:**
- Git commit with message: `perf(bundling): implement code splitting and dynamic module loading`
- Initial bundle size < 15KB, page modules < 10KB each

---

### Task 15: Service Worker & Offline Support

**Description:**
Implement a service worker to enable offline browsing and cache management.

Create `public/service-worker.js` that:
- Caches the shell (HTML, CSS, JS modules) on first load
- Caches API responses (`portfolio.json`) with a stale-while-revalidate strategy
- Enables offline viewing of previously visited pages
- Shows a banner when the user is offline
- Updates cached content in the background

Create `modules/performance/OfflineManager.ts` that:
- Registers the service worker
- Listens to online/offline events
- Shows/hides an offline banner
- Manages cache versioning and cleanup

Add a cache versioning system so old caches are cleaned up automatically when you deploy a new version.

**Acceptance Criteria:**
- [ ] Service worker registers successfully
- [ ] Offline banner appears when disconnected
- [ ] Previously visited pages load offline
- [ ] Images and content load from cache offline
- [ ] Online status bar disappears when connection restored
- [ ] New deployments clear old caches
- [ ] Cache size stays reasonable (< 5MB)
- [ ] No errors in console for offline operations

**Definition of Done:**
- Git commit with message: `feat(offline): implement service worker and offline support`
- Portfolio functions fully offline after first visit
- Cache cleaning script in build process

---

### Task 16: Analytics & Performance Monitoring

**Description:**
Add lightweight analytics to understand how visitors interact with your portfolio.

Implement `modules/analytics/Analytics.ts` that tracks:
- Page views (which pages are visited most)
- Search queries (what visitors search for)
- Time on page
- Click events (which links are clicked)
- Performance metrics (LCP, FID, CLS)

Send metrics to a lightweight backend (or use a third-party service like Plausible or Fathom). The implementation should:
- Be privacy-respecting (no personal data, no tracking cookies)
- Batch events for efficiency
- Not block page rendering
- Fall back gracefully if analytics service is unavailable

Create a `/api/analytics` endpoint (if using a backend) that receives event batches and stores them.

Display a "Portfolio Analytics" dashboard in a `src/pages/AnalyticsDashboard.tsx` (admin-only, accessible via a secret URL) showing:
- Total visitors this month
- Top pages
- Popular search terms
- Performance metrics trends

**Acceptance Criteria:**
- [ ] Page views are tracked and aggregated
- [ ] Search queries are logged (without personally identifying info)
- [ ] Performance metrics are collected
- [ ] Analytics doesn't slow down page load
- [ ] Analytics dashboard shows accurate data
- [ ] No sensitive data is collected or stored
- [ ] Analytics can be disabled via environment variable

**Definition of Done:**
- Git commit with message: `feat(analytics): add lightweight analytics and monitoring`
- Analytics dashboard URL documented in `ANALYTICS.md`

---

### Task 17: SEO & Open Graph Metadata

**Description:**
Optimize the portfolio for search engines and social media sharing.

Update each HTML page with:
- Proper `<title>` tags (different for each page)
- Meta descriptions (160 characters, descriptive)
- Open Graph tags (`og:title`, `og:description`, `og:image`, `og:url`)
- Twitter Card tags (`twitter:card`, `twitter:title`, `twitter:description`, `twitter:image`)
- Canonical URLs
- Structured data (`schema.org` JSON-LD for Person, BreadcrumbList)

Create `modules/seo/SEOManager.ts` that:
- Dynamically updates meta tags based on the current page
- Generates Open Graph tags from `portfolio.json`
- Handles dynamic page title updates

Create a `robots.txt` and `sitemap.xml` in the public directory. Document best practices in `docs/SEO.md`.

**Acceptance Criteria:**
- [ ] Each page has unique, descriptive `<title>` and `<meta name="description">`
- [ ] Open Graph tags are correct for each page
- [ ] Twitter Card tags are correct
- [ ] Structured data validates in Schema.org validator
- [ ] Sitemap lists all pages and updates automatically
- [ ] Canonical URLs prevent duplicate content
- [ ] Lighthouse SEO score: 90+
- [ ] Social media sharing displays correct previews

**Definition of Done:**
- Git commit with message: `feat(seo): add comprehensive SEO and social media optimization`
- Updated `docs/SEO.md` with best practices
- Lighthouse SEO audit: 90+

---

### Task 18: Phase 3 Performance & Feature Validation

**Description:**
Complete the test suite with `tests/phase-3.test.ts` covering all Phase 3 features and performance goals.

Write assertions for:
1. **Lazy Loading:** Verify images load lazily, no layout shift, LQIP displays
2. **Code Splitting:** Initial bundle < 15KB, page modules < 10KB, modules load < 100ms
3. **Service Worker:** Offline mode works, cache strategy is correct, old caches cleaned
4. **Analytics:** Events are tracked and batched correctly, no performance impact
5. **SEO:** Meta tags are present and correct, structured data validates, Lighthouse score 90+
6. **Performance:** LCP < 2s, FID < 100ms, CLS < 0.1, Time to Interactive < 2s

Run the full test suite and generate a performance report:
```
Performance Report (Phase 3 Complete)
─────────────────────────────────
✅ Initial Bundle Size: 14.2KB gzipped
✅ Page Load Time: 1.8s (LCP)
✅ First Input Delay: 45ms (FID)
✅ Cumulative Layout Shift: 0.05 (CLS)
✅ Time to Interactive: 1.6s
✅ Service Worker: Enabled
✅ Offline Support: Working
✅ SEO Score: 95/100
✅ Accessibility Score: 98/100
✅ All 18/18 E2E tests passing
```

**Acceptance Criteria:**
- [ ] `tests/phase-3.test.ts` exists with 6+ assertions
- [ ] Running `npm test` passes all Phase 1 + 2 + 3 assertions (18+ total)
- [ ] Performance report generated and meets all targets
- [ ] Lighthouse audit shows 90+ on all metrics
- [ ] No console errors or warnings on any page
- [ ] Full E2E test coverage across all features
- [ ] README updated with final status and performance metrics

**Definition of Done:**
- Git commit with message: `test(phase-3): complete comprehensive E2E validation and performance audit`
- Final status: ✅ All 3 Phases Complete (18/18 tasks delivered)
- Performance report published in `PERFORMANCE.md`

---

## Architecture Diagram

```
Lucas Kara Portfolio
├── public/
│   ├── index.html (search landing page)
│   ├── career.html (minimal skeleton)
│   ├── projects.html (minimal skeleton)
│   ├── qualifications.html (minimal skeleton)
│   ├── service-worker.js
│   ├── robots.txt
│   └── sitemap.xml
├── src/
│   ├── modules/
│   │   ├── index.ts (entry point)
│   │   ├── theme/
│   │   │   └── DarkMode.ts
│   │   ├── modal/
│   │   │   └── ModalManager.ts
│   │   ├── navigation/
│   │   │   ├── AppsDrawer.ts
│   │   │   └── Breadcrumbs.ts
│   │   ├── ui/
│   │   │   └── UIUtils.ts
│   │   ├── content/
│   │   │   └── ContentLoader.ts
│   │   ├── search/
│   │   │   ├── SearchEngine.ts
│   │   │   └── SearchUI.ts
│   │   ├── render/
│   │   │   └── PageRenderer.ts
│   │   ├── performance/
│   │   │   ├── LazyLoader.ts
│   │   │   ├── OfflineManager.ts
│   │   │   └── ModuleLoader.ts
│   │   ├── analytics/
│   │   │   └── Analytics.ts
│   │   └── seo/
│   │       └── SEOManager.ts
│   └── styles/
│       ├── global.css
│       ├── theme.css
│       ├── animations.css
│       └── responsive.css
├── data/
│   └── portfolio.json (semantic content source)
├── config/
│   └── apps.ts (app grid configuration)
├── tests/
│   ├── phase-1.test.ts
│   ├── phase-2.test.ts
│   └── phase-3.test.ts
├── docs/
│   ├── ARCHITECTURE.md
│   ├── SEO.md
│   ├── PERFORMANCE.md
│   └── DEVELOPMENT.md
├── dist/
│   ├── bundle.js (main entry point)
│   ├── career.module.js
│   ├── projects.module.js
│   └── qualifications.module.js
├── package.json
├── tsconfig.json
├── esbuild.config.js
└── README.md
```

---

## Success Metrics

| Metric | Target | Phase | Status |
|--------|--------|-------|--------|
| Initial Bundle Size | < 15KB | Phase 3 | — |
| Page Load Time (LCP) | < 2s | Phase 3 | — |
| First Input Delay | < 100ms | Phase 3 | — |
| Cumulative Layout Shift | < 0.1 | Phase 3 | — |
| Time to Interactive | < 2s | Phase 3 | — |
| Lighthouse Score | 90+ | Phase 3 | — |
| Accessibility Score | 95+ | Phase 3 | — |
| E2E Test Coverage | 100% | Phase 3 | — |
| Service Worker | Enabled | Phase 3 | — |
| Offline Support | Full | Phase 3 | — |
| SEO Coverage | 100% | Phase 3 | — |

---

## Phase Transition Criteria

**Phase 1 → Phase 2:**
- All 6 Phase 1 tasks completed
- All Phase 1 E2E tests passing
- No regressions (pages function identically to original)
- Code review approved

**Phase 2 → Phase 3:**
- All 6 Phase 2 tasks completed
- All Phase 1 + 2 E2E tests passing
- Search functionality working on all pages
- Content architecture validated

**Phase 3 → Release:**
- All 6 Phase 3 tasks completed
- All Phase 1 + 2 + 3 E2E tests passing
- Performance targets met (Lighthouse 90+)
- SEO fully optimized
- Accessibility audit passed
- Documentation complete

---

## Development Workflow

```bash
# Setup
git clone https://github.com/wireworks-operations/Lucas-Kara.git
cd Lucas-Kara
npm install

# Development
npm run dev              # Watch and rebuild modules
npm test                 # Run full test suite
npm run lint             # TypeScript and eslint
npm run build            # Production build
npm run analyze          # Bundle size analysis

# Deployment
npm run deploy           # Deploy to GitHub Pages
```

---

## Next Steps (Immediate Actions)

1. **Complete Task 01:** Set up package.json, esbuild, and module directory structure
2. **Create this file:** Commit ROADMAP.md to the repo root
3. **Initialize TypeScript:** Configure tsconfig.json for ES6 module output
4. **First commit:** `Initial: ES6 module foundation (Task 01)`

**Target Completion:** Phase 1 by end of next sprint (~2-3 weeks)

---

## Notes & Assumptions

- **No Framework:** This roadmap maintains the "vanilla JS + HTML" philosophy. No React, Vue, or heavy frameworks.
- **ES6 Modules Only:** All code uses native ES6 modules (`import`/`export`). No CommonJS.
- **Performance First:** Every task includes a performance consideration (bundle size, render time, etc.).
- **Search-First UX:** The portfolio is designed around the search metaphor introduced by the Google Search-like design.
- **Offline-Ready:** Phase 3 includes service worker support for offline browsing.
- **Fully Tested:** Every phase includes E2E tests to catch regressions and ensure quality.

---

**Roadmap Maintained by:** You (@wireworks-operations)  
**Last Updated:** 2026-05-25  
**Version:** 1.0.0

