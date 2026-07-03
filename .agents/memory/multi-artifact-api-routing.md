---
name: Multi-artifact frontend/backend API routing
description: How frontend artifacts should call a separate backend API artifact in this Replit multi-artifact setup.
---

When a web frontend artifact (e.g. React+Vite) and an API artifact (e.g. Express) are registered as
separate artifacts with different local ports, the frontend must call the API using **relative paths**
(e.g. `fetch("/api/menu")`), not an absolute URL with a hardcoded port.

**Why:** Each artifact's `artifact.toml` declares a `paths` prefix (e.g. `["/api"]`) that the shared
Replit proxy uses for path-based routing on the single public domain/port. A relative `/api/...` request
from the browser is routed by the proxy to the correct backend artifact automatically. Hardcoding a port
or calling `setBaseUrl()` (which is meant for React Native/Expo apps that have no shared proxy) breaks in
the actual preview/production domain even if it happens to work when curling `localhost:<port>` directly
in the dev sandbox.

**How to apply:** When restoring, debugging, or building a multi-artifact app where a frontend calls a
sibling API artifact, verify calls use relative paths matching the API artifact's `paths` prefix. If a
generated API client (e.g. orval) supports a base-url override, leave it unset for web artifacts.
