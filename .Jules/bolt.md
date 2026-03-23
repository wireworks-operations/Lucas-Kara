## 2025-03-23 - [Runtime Search Optimization]
**Learning:** In a single-page portfolio with dynamic filtering, repeatedly querying the DOM for result elements and their text content on every keystroke or filter click causes unnecessary layout thrashing and CPU load. Caching these elements and their searchable text once at startup or on first use significantly improves runtime performance and responsiveness.
**Action:** Cache DOM references for frequently accessed elements in interactive components. Pre-calculate searchable text for local search implementations to avoid repeated `.textContent` and `.querySelector` calls.

## 2025-05-15 - [Search Debouncing & Single-Pass Optimization]
**Learning:** Real-time search inputs that trigger UI updates on every keystroke cause redundant processing and layout thrashing, especially during rapid typing. Implementing a 150ms debounce window significantly reduces CPU load. Additionally, refactoring filtering and result counting into a single O(N) pass further improves efficiency by eliminating redundant iterations.
**Action:** Use a debounce utility for all real-time search and filter inputs. Consolidate filtering and counting logic into a single loop to minimize iterations over large datasets.
