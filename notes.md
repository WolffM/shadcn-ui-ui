## Steps to reproduce

1. Ran `corepack enable && pnpm install` in the repository root.
2. Built the CLI with `pnpm --filter=shadcn build`.
3. Started the local registry with `pnpm --filter=v4 dev`.
4. Copied the `next-app-init` fixture into a temporary working directory.
5. Updated that fixture’s `components.json` to use `@/custom-utils` for `aliases.utils` and added a matching `@/custom-utils` path entry in `tsconfig.json`.
6. Ran `node packages/shadcn/dist/index.js add sidebar --yes` with `REGISTRY_URL=http://localhost:4000/r` and the repository `templates` directory configured for the CLI.

## Observed

The generated `components/ui/sidebar.tsx` and `components/ui/button.tsx` imported `cn` from `@/custom-utils`, not from the default `@/lib/utils`.

I repeated the same manual check with `next-app-imports`, `#custom-utils`, and the monorepo fixture. In each case, the installed sidebar continued to honor the configured utils alias.

Because I could not reproduce the reported breakage on the current checkout, I added a dedicated regression test to lock this behavior down.

## Expected

When `components.json` defines a custom `aliases.utils` value, installed components should use that exact alias in their generated imports. For the affected `sidebar` flow, both the main sidebar file and dependent UI files such as `button.tsx` should reflect the configured utils alias consistently, without falling back to the default `@/lib/utils` path or leaving unresolved registry imports behind.
