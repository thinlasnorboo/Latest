---
  name: Express 5 wildcard route params are arrays
  description: Express 5 (path-to-regexp v8) turns named wildcard params like *path into arrays of segments, not slash-joined strings — silently breaks multi-segment path logic (e.g. object storage file lookups) with no thrown error, just wrong results (404s).
  ---

  When a route is defined as `router.get('/foo/*path', ...)`, `req.params.path` is an **array** of path segments in Express 5, not a single string with slashes as it was in Express 4.

  **Why:** Code that does `'/prefix/' + req.params.path` silently produces a comma-joined string (e.g. `uploads,abc123` instead of `uploads/abc123`) because JS coerces arrays via `.toString()` on string concatenation. This doesn't throw — it just causes downstream lookups (file existence checks, storage paths) to always fail, which manifested as an object-storage upload succeeding but every retrieval 404ing.

  **How to apply:** Whenever a wildcard/catch-all route param is consumed, join it explicitly: `Array.isArray(raw) ? raw.join('/') : String(raw)`. Check this any time debugging mysterious 404s or not-found errors on catch-all routes in an Express 5 app.
  