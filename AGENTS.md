# Agent Instructions — Paseo

Paseo is a mobile/web/desktop control plane for local coding agents. It is an npm
workspace monorepo: `packages/server`, `app`, `cli`, `relay`, `desktop`, and `website`.

`docs/` is the source of truth. Start non-trivial work by listing it and reading the
relevant files—especially `architecture.md`, `agent-lifecycle.md`, `data-model.md`,
`glossary.md`, `coding-standards.md`, `development.md`, `testing.md`, and the
platform/feature-specific doc. `docs/AGENT-REFERENCE.md` preserves the full routing table,
platform matrix, and debugging detail.

## Commands

```bash
npm run dev
npm run dev:app
npm run dev:desktop
npm run cli -- ls -a -g
npm run typecheck
npm run lint
npm run format
```

Checkout-local development uses `.dev/paseo-home`; the packaged daemon uses `~/.paseo`
on port 6767.

## Non-negotiable rules

- Never restart the main daemon on port 6767 without permission; it manages running
  agents and may be the process controlling you.
- Do not treat a timeout as evidence that a restart is needed.
- Do not run the full test suite locally. Run the specific changed test with
  `npx vitest run <file> --bail=1`; broad verification belongs in CI.
- Always run `npm run typecheck` and `npm run lint`; use npm scripts for lint/format.
- Rebuild the owning workspace stack before patching types that may come from stale
  generated declarations (`build:client` or `build:server`).
- Agent-provider authentication belongs to providers; do not add auth checks to tests.

## Contracts and design

- The wire protocol remains bidirectionally parseable across old/new client and daemon
  versions: new fields optional/defaulted, removed fields still accepted, types never
  narrowed. New features may require a capability; do not simulate them through parallel
  fallback RPC paths.
- Capability checks happen once through `server_info.features.*`. Tag genuine temporary
  shims `COMPAT(name)` with introduction version and removal date.
- New RPCs use dotted namespaces and `.request`/`.response` pairs.
- State schemas are Zod-validated and file writes atomic. UI terminology follows
  `docs/glossary.md` exactly.
- Cross-platform is the default. Use `isWeb`, `isNative`, `getIsElectron()`, or
  `useIsCompactFormFactor()` only for their documented capability. Prefer `.web`,
  `.native`, and `.electron` modules for substantial platform divergence.
- Never use raw DOM APIs without a web gate or platform as a proxy for form factor.

Full hover, Unistyles, floating-panel, terminal-performance, provider, relay, release,
and mobile-testing rules live in their named docs; do not duplicate them here.
