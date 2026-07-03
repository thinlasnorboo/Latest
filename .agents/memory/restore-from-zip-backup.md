---
name: Restoring a full-stack project from a zip backup
description: Checklist and gotchas when reviving a full-stack app (frontend + API + DB + generated code) from an old backup zip after a workspace reverted to an empty scaffold.
---

When a workspace has reverted to an empty scaffold and the user provides a zip backup of a previously
working full-stack app, restoring it is more than copying `src/` — several easy-to-miss pieces break the
build silently until you check each:

1. Diff the extracted backup against the current empty scaffold first to get a full list of missing
   files/folders/packages, not just the obviously-named ones (routes, schema, generated clients).
2. Create a proper artifact (via the artifacts flow) for the frontend rather than dumping files into a
   raw directory — this wires up the correct port/proxy config automatically.
3. Copy backend routes, lib files, DB schema, OpenAPI spec, and orval-generated client/zod packages — the
   generated code must match the restored `openapi.yaml`, so re-run codegen after copying to be safe.
4. Watch for **whole subdirectories under `src/` that are easy to miss** in an ad hoc file-by-file
   restore (e.g. a `context/` folder for a React Context provider) — a missing directory only surfaces as
   a Vite "failed to resolve import" error at runtime, not at copy time. Always diff the full backup
   `src/` directory listing against what was restored, not just the files referenced in the initial diff.
5. Don't blindly overwrite root `pnpm-workspace.yaml` / root `package.json` from the backup — root config
   (security settings like `minimumReleaseAge`, overrides) may have evolved independently of the app
   code. Merge dependencies at the package level instead.
6. After restoring: `pnpm install` → provision/push DB schema → run API codegen → restart all affected
   workflows → screenshot multiple pages (not just the homepage) to catch pages whose data-fetching routes
   are broken (e.g. a route path mismatch surfaces as blank skeleton loaders with silent 404s in the
   console, not a build error).
