# Decision Log: Lucas Kara Portfolio

This document captures key decisions and *why* I made them. Not to justify them, but to make thinking visible.

---

## Decision 1: Google Search as the Interface Metaphor

**Date:** 2026-05-25  
**Status:** Implemented

### The Question
How do I make my portfolio *discoverable*? Most portfolios are navigational (menu → page), which assumes visitors know what they're looking for.

### What I Considered
- Traditional navigation (About → Career → Projects)
- Interactive timeline
- Tag-based categorization
- **Google Search interface** (what I chose)

### Why I Chose Search
I work in operations. When I need something, I search for it. That's how I think. So the portfolio mirrors that: "What do you want to know about me? Search for it."

This isn't just cosmetic—it's a different *mental model* of the portfolio. It says: "I'm organized by *intent*, not by hierarchy."

### Trade-off
New visitors might not immediately understand they can search. Might need explicit UX hints (placeholder text, suggestions).

### How I'd Evaluate This
- Do people actually search, or do they navigate?
- Do search results help them understand my work?
- Does this interface encourage exploration?

---

## Decision 2: ES6 Modules Over React/Vue

**Date:** 2026-05-24  
**Status:** In Progress (Phase 1)

### The Question
How do I refactor 60KB of inline `<script>` blocks into maintainable, testable code?

### What I Considered
- React + TypeScript
- Vue + TypeScript
- **ES6 Modules + TypeScript** (what I chose)

### Why ES6 Modules

**Reason 1: HTML Remains Visible**  
With React or Vue, the HTML gets compiled into JSX and a virtual DOM. For a portfolio, that's a liability. Someone should be able to inspect the source and understand the structure. React hides that.

**Reason 2: Performance Budget**  
A portfolio has zero excuse to be slow. React adds ~35KB to the bundle. My entire portfolio should be < 15KB gzipped. Not possible with React.

**Reason 3: No State Complexity**  
I have maybe 5 interactive elements: theme toggle, modal opens, search, accordion. That's not worth a state framework. Manual state is actually simpler.

**Reason 4: Maintenance**  
5 years from now, someone (maybe me, maybe someone else) will need to modify this. ES6 is just JavaScript. React is "React." The maintenance burden is different.

### Trade-off
Manual state management can feel verbose. But it's explicit, testable, and doesn't hide anything.

### How I'd Evaluate This
- Is the code harder to understand than React would be?
- Did we hit the 15KB bundle target?
- Can someone unfamiliar with React still modify this?

---

## Decision 3: Client-Side Search + Service Worker Caching

**Date:** 2026-05-25  
**Status:** Planned (Phase 2)

### The Question
How do I make search instant, work offline, and never make a network call?

### What I Considered
- Server-side search (API calls for every query)
- Local IndexedDB (complex setup, async API)
- **Client-side search + Service Worker cache** (what I chose)

### Why This Approach

**Offline-first mindset.** I built the Wire Tools Suite for warehouse operators. They don't have reliable network. That taught me: tools must work without internet.

**Search is instant.** No network latency. Type "automation", get results in < 50ms.

**Cache is automatic.** Service Worker caches the JSON on first visit. Every subsequent visit uses the cache.

### How It Works
1. Load `portfolio.json` (tiny, ~5KB)
2. Service Worker caches it
3. Search engine loads data into memory
4. Search runs locally
5. If offline, everything still works

### Trade-off
If the portfolio grows to 50K items, local search might get slow. Solution: Add an inverted index or move to server-side search. The interface doesn't change.

### How I'd Evaluate This
- Does search feel instant?
- Does offline mode work?
- What's the actual cache size?

---

## Decision 4: Semantic JSON Over Database

**Date:** 2026-05-25  
**Status:** Planned (Phase 2)

### The Question
Where do I store portfolio content? Database, markdown files, JSON, CMS?

### What I Considered
- Markdown files (hard to search)
- Firebase/Supabase (overkill, requires backend)
- Custom database (too much overhead)
- **Semantic JSON file** (what I chose)

### Why JSON

**Simple to version.** It's just a file in git. I can see the history.

**Easy to search.** JSON can be indexed and queried without a database.

**Migration path.** If I need a database later, the JSON schema becomes the database schema. No rework.

**Portable.** Someone could fork this, modify the JSON, and have a different portfolio. That's cool.

### The Structure
```json
{
  "career": [
    {
      "id": "eecol-wire",
      "title": "...",
      "tags": ["operations", "automation"],
      "description": "..."
    }
  ]
}
```

Tags are crucial. They're the index. Every item is tagged so search can find it by concept, not just keyword matching.

### Trade-off
With JSON, I manually manage content. No CMS, no admin panel. That's fine for a portfolio. If this scales to a full product, I'd add a CMS layer.

### How I'd Evaluate This
- Is the JSON easy to update?
- Does the schema capture everything I need?
- Is search finding things by tag effectively?

---

## Decision 5: No CSS Framework, Hand-Written Styles

**Date:** 2026-05-20  
**Status:** Implemented

### The Question
Should I use Tailwind, Bootstrap, or hand-write CSS?

### What I Considered
- Bootstrap (too heavy, too much I don't use)
- Tailwind (adds complexity, still requires config)
- **Hand-written CSS with variables** (what I chose)

### Why Hand-Written CSS

**I want to understand every pixel.** When I see a shadow or a color, I want to know why it's there. CSS variables make that explicit:
```css
--shadow-md: 0 4px 6px rgba(0, 0, 0, 0.07), 0 2px 4px rgba(0, 0, 0, 0.05);
```

**Google Search aesthetic requires precision.** That aesthetic is specific and deliberate. A CSS framework would fight me.

**Bundle size.** Hand-written CSS is smaller than any framework.

### How It's Organized
- Global variables (colors, shadows, spacing)
- Component-scoped styles (modal, cards, buttons)
- Responsive breakpoints
- Light/dark mode variants

### Trade-off
It's more work upfront. But I maintain complete control and the result is exactly what I intended.

### How I'd Evaluate This
- Does the aesthetic feel cohesive?
- Is CSS maintainable?
- Does the bundle size reflect the simplicity?

---

## Decision 6: Dark Mode as Default (Not Light)

**Date:** 2026-05-20  
**Status:** Implemented

### The Question
What should the default theme be?

### What I Chose
Ship with dark mode enabled. Respect `prefers-color-scheme` if set, but default to dark.

### Why

**Operational reality.** Warehouse workers use tools at night, under fluorescent lights. Dark mode reduces eye strain.

**Reflects my perspective.** I work warehouse operations, not a corporate office. Dark mode fits.

**Technical precedent.** Wire Tools Suite defaults to dark mode. Consistency matters.

### How It Works
```javascript
const isDarkMode = localStorage.getItem('dark-mode') !== 'false';
document.body.classList.toggle('dark-mode', isDarkMode);
```

Respects user preference, but assumes dark is the starting point.

### How I'd Evaluate This
- Do new visitors feel comfortable with dark mode?
- Is light mode accessible and polished as a fallback?
- Does this choice reflect my aesthetic?

---

## Decisions I'm Still Uncertain About

### Should I Add a Blog?
- **Pro:** Demonstrate thinking, SEO, narrative
- **Con:** More content to maintain, could dilute focus
- **Undecided:** Waiting to see if there's demand

### Should I Build an Admin Panel?
- **Pro:** Could update portfolio without git commits
- **Con:** Adds complexity, encourages careless updates
- **Leaning No:** Git commits force intentionality

### Should I Add Analytics?
- **Pro:** Understand who visits and what they search for
- **Con:** Privacy concerns, adds overhead
- **Leaning Yes:** Privacy-respecting analytics only (Plausible, not Google)

---

## How This Decision Log Evolves

As I build Phase 2 and 3, I'll add more decisions here. The goal isn't to create a perfect record—it's to make thinking visible. Someone reading this should understand not just *what* I built, but *why*.
