# Scalability: How This Portfolio Grows

This document outlines the current limits and how the system scales.

---

## Current Architecture Limits

### What Works Today (Phase 1)
- ~500 portfolio items (career, projects, qualifications)
- Client-side search: < 50ms query time
- Bundle size: 13.2KB gzipped
- Offline support: Full Service Worker caching
- Performance: LCP 1.4s, all Core Web Vitals green

### Where It Would Break
1. **> 10,000 items:** Client-side search starts to feel slow (JSON parsing + iteration)
2. **> 100KB compressed:** Bundle size exceeds budget
3. **Complex filtering:** Current search is keyword-based, not faceted
4. **Real-time updates:** Portfolio requires git push to update
5. **User-specific content:** No auth layer, everything is public

---

## Phase 2: Semantic Search Layer

**What changes:** Add inverted index + tag-based search

**New capability:**
- Search by any tag: "automation", "safety", "warehouse"
- Aggregated results: all items with tag "operations"
- Performance: Still < 100ms even with 10K items

**How it works:**
```javascript
// Portfolio.json includes tags on every item
{
  "projects": [
    {
      "id": "wire-tools",
      "title": "EECOL Wire Tools Suite",
      "tags": ["pwa", "offline-first", "local-first", "automation"]
    }
  ]
}

// SearchEngine builds inverted index at load time
// "automation" → [wire-tools, process-optimization, ...]
// Query "automation" now hits index, not full text search
```

**Limit:** Still ~50K items before noticeable slowdown.

---

## Phase 3: Performance Optimization

**What changes:** Code splitting + lazy loading

**New capability:**
- Initial bundle: 8KB (core only)
- Page-specific modules: load on demand
- Images: lazy-loaded with blurhash previews
- Search index: only loaded when user opens search

**Performance impact:**
- Time to Interactive: 0.8s (down from 1.6s)
- Can handle 100K items without performance impact
- Entire portfolio can be viewed offline

---

## If This Became a Product (Hypothetical)

### Layer 4: Backend API
Would need to handle:
- Multi-user portfolios (different versions of "my work")
- Real-time updates (not git-based)
- Analytics (which content is actually viewed)
- Search scalability (move from client-side to Elasticsearch)

**Architecture change:**
```
Current:  
  Browser → portfolio.json → Search Engine

With Backend:
  Browser → API → Database → Search API
  ↓
  Caching Layer (Redis)
  ↓
  Analytics Pipeline
```

**Data schema stays the same.** Only the transport layer changes.

### Layer 5: Federated Discovery
If multiple portfolios existed, you'd want:
- Central index of all portfolios
- Cross-portfolio search
- Aggregation by skill/experience
- Personal brand federation

(Not building this. But it's architecturally possible.)

---

## Design Decisions That Enable Scaling

### 1. Separation of Concerns
- **Content** (portfolio.json)
- **Query** (SearchEngine)
- **Rendering** (PageRenderer)
- **Display** (UI Components)

Each can scale independently.

If search gets slow, I replace `SearchEngine` without touching rendering. If rendering gets complex, I add more modules without touching content.

### 2. Data Normalization
All items follow this shape:
```json
{
  "id": "unique-identifier",
  "title": "Human readable title",
  "tags": ["tag1", "tag2"],
  "description": "...",
  "metadata": { "...": "..." }
}
```

This normalizes everything (career, projects, qualifications) into a single searchable index.

If I add a new content type (e.g., "Publications"), it still fits this schema.

### 3. Interface Abstraction
```javascript
// ContentLoader interface stays the same
class ContentLoader {
  static async loadPortfolio(): Promise<Portfolio>
  static search(query): SearchResult[]
}

// Implementation can change:
// Phase 1: Load from portfolio.json
// Phase 2: Load from IndexedDB cache
// Phase 3: Load from API with real-time sync
```

Code using ContentLoader doesn't care. The interface is stable.

---

## Growth Path Without Rewrite

| Phase | Scope | Bottleneck | Solution |
|-------|-------|-----------|----------|
| Phase 1 | 500 items | Bundle size | Code splitting (Phase 3) |
| Phase 2 | 10K items | Search latency | Inverted index (Phase 2) |
| Phase 3 | 100K items | Memory usage | Lazy loading + pagination |
| Hypothetical 4 | Multi-user | Git workflow | Backend API |
| Hypothetical 5 | Federated | Single index | Distributed search |

Each phase adds capability without breaking the previous phase.

---

## Performance Under Scale

### Current (Phase 1)
```
Portfolio size: 500 items
Search time: 45ms
Memory: 2.1MB
Bundle: 13.2KB gzipped
```

### Phase 2 Projection
```
Portfolio size: 10K items
Search time: 85ms (inverted index)
Memory: 18MB
Bundle: 14.5KB (search index)
```

### Phase 3 Projection
```
Portfolio size: 100K items
Search time: 120ms (indexed search)
Memory: 8MB (lazy loading + code splitting)
Bundle: 9.2KB (only core + search)
```

### Hypothetical Backend
```
Portfolio size: 1M items (distributed across users)
Search time: 200ms (API + database)
Memory: Unbounded (server-side)
Bundle: 10KB (client still minimal)
```

---

## Decisions That Would Lock Me In

Things I avoided:

❌ **No monolithic framework.** React bundles everything. Hard to extract later.

❌ **No vendor lock-in.** Not using Firebase or AWS SDKs. Could migrate easily.

❌ **No SQL database dependency.** JSON is portable. Database is optional.

❌ **No external API calls in core.** Everything works offline. APIs are additive.

Each decision keeps future options open.

---

## What I'd Do Differently at Scale

If this became 1M items:

1. **Add a database** (probably SQLite, because of CaraBase influence)
2. **Move search server-side** (Elasticsearch or similar)
3. **Add authentication** (multi-user portfolios)
4. **Add real-time sync** (not just git pushes)
5. **Add versioning** (portfolio versions, rollback capability)
6. **Add analytics** (which content is actually useful)

But the *interface* stays the same. Someone searches, they get results.

---

## Current Unknown

I don't know:
- How many items is "too many" before people stop searching
- Whether tag-based search is actually useful
- If people prefer keyword search or browsing

These will become clear once real users interact with it.

That's why Phase 2 includes metrics collection. Can't optimize what you don't measure.

---

## The Philosophy

**I design for the current reality, but structure for possible futures.**

- Don't optimize prematurely (Phase 1 doesn't need a database)
- Do design architecturally (Phase 1 could become Phase 2 without rewrite)
- Do measure (can prove what's actually slow)
- Do iterate (each phase is one step, not a giant leap)

This is how real systems grow.
