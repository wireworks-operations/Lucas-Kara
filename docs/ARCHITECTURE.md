# Architecture: Lucas Kara Portfolio

## The Metaphor

This portfolio uses **Google Search as the interface metaphor**. Not because it looks cool, but because it reflects how I think about knowledge: *things are discoverable through intent, not navigation*.

Instead of a traditional "About → Projects → Skills" hierarchy, everything funnels through search. You're looking for something about me (a skill, a project, an experience), and the interface helps you find it.

---

## Why This Matters

Most portfolios are **destination-based**: "Click Projects, scroll until you find wire tools."

This one is **query-based**: Type "automation", get projects + career highlights + skills that relate. Type "warehouse", get operational experience + tools built for operations.

That difference is architectural, not cosmetic.

---

## The Stack Decision: ES6 Modules (Not React)

### The Options I Considered

| Aspect | React | Vue | ES6 Modules |
|--------|-------|-----|------------|
| Bundle Size | ~35KB | ~28KB | ~14KB ✓ |
| HTML Stays Visible | No | Partially | Yes ✓ |
| Can Inspect Markup | Hard | Medium | Easy ✓ |
| State Management | Built-in | Built-in | Manual |
| Semantic Preservation | Broken by JSX | Mostly OK | Perfect ✓ |

### Why I Chose ES6 Modules

1. **HTML is a first-class citizen here.** The Google Search aesthetic only works if the HTML is visible, semantic, and inspectable. React hides it in JSX and a virtual DOM.

2. **Performance budget.** A portfolio has no excuse for being slow. 15KB gzipped is achievable with ES6 modules. React or Vue would blow that immediately.

3. **No state complexity.** This portfolio is mostly static content with a few interactions (theme toggle, modal opens, search). That's not worth the complexity of a state framework.

4. **Maintenance burden.** React has a learning curve. ES6 modules? Anyone who knows JavaScript can read and modify this. That respects the person who inherits my code.

### The Trade-off

Manual state management feels tedious at first. But:
- I'm not fighting a framework's opinions
- I can make weird decisions without asking "will React let me do this?"
- The codebase stays small enough to keep in my head

---

## Content Architecture: Semantic JSON

All portfolio content lives in `data/portfolio.json`:

```json
{
  "profile": { "name": "Lucas Kara", ... },
  "career": [
    {
      "id": "eecol-wire-cutting",
      "title": "Wire Cutter & Material Handler",
      "tags": ["operations", "automation", "precision", "safety"],
      "description": "..."
    }
  ],
  "projects": [...],
  "qualifications": [...]
}
```

**Why?**

- Single source of truth for all content
- Search engine can index everything uniformly
- If I ever move to a database, the interface stays the same
- Easy to version and track changes

---

## The Search Engine: Client-Side, Offline-First

Search runs **entirely in the browser**:

1. Portfolio JSON loads once on first visit
2. Service Worker caches it
3. Search runs locally against the cache
4. No network calls needed

**Why this approach?**

I learned from Wire Tools Suite that offline capability is table stakes. Users expect tools to work even when the network is flaky. Plus, client-side search is instant—no latency waiting for a server.

**When would this break?**

With ~10,000 portfolio items, search would start to feel slow. But I don't anticipate that. If it does, I can move to an inverted index or a lightweight Elasticsearch instance. The interface doesn't change.

---

## Module Organization

```
modules/
├── theme/          (Dark mode, color schemes)
├── modal/          (Dialog system)
├── navigation/     (App drawer, breadcrumbs)
├── search/         (Search engine + UI)
├── content/        (JSON loading, caching)
├── render/         (Page templates)
└── performance/    (Lazy loading, service worker)
```

Each module is **self-contained and testable**. 

- `DarkMode.ts` knows nothing about search
- `SearchEngine.ts` knows nothing about the UI
- Everything communicates through simple interfaces

This makes it easy to test, debug, and replace pieces without breaking the whole system.

---

## Performance Constraints

- **Initial bundle:** < 15KB gzipped
- **Page load (LCP):** < 2s
- **Search response:** < 50ms
- **No layout shift (CLS):** < 0.1

These aren't arbitrary. They reflect real-world constraints:

- People on slow networks should load in under 2 seconds
- Search should feel instant (not noticeable delay)
- Content should never jump around (terrible UX)

If I add features that violate these, I should feel it immediately. That's the value of constraints.

---

## What This Portfolio is NOT

- **Not a product showcase.** It's not "look how shiny I am"
- **Not a marketing site.** It's not trying to convince you I'm the best
- **Not a traditional resume.** It's not a list of buzzwords

It's a **thinking artifact**. It shows how I approach problems, what constraints I respect, and how I ship.

---

## Next Phases

**Phase 2** adds semantic content architecture: full-text search, dynamic rendering, breadcrumbs.

**Phase 3** adds performance: lazy loading, code splitting, service workers, SEO.

Each phase builds on the previous one without breaking the foundation. That's good architecture.
