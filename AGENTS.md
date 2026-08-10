# Repository guidelines

## Tooling

- Use pnpm; do not use npm or yarn.
- Run workspace commands from the repository root.
- Use Oxlint and Oxfmt. Do not add app-local ESLint, Prettier, Oxlint, or Oxfmt configuration without a concrete app-specific need.

## Verification

Before opening a pull request, run:

```bash
pnpm format
pnpm lint
pnpm check-types
pnpm build
```

Run API tests when changing the API:

```bash
pnpm --filter api test
pnpm --filter api test:e2e
```

## Documentation

- Read the relevant `docs/` documentation before making changes when it applies to the task.

## Architecture

- Keep API changes within `apps/api` and web changes within `apps/web` unless a shared workspace concern requires otherwise.
- Keep generated TanStack Router route trees unchanged; they are generated artifacts.
