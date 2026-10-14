# pattobrien/vite-plugin-react

This repository is a fork of [vitejs/vite-plugin-react](https://github.com/vitejs/vite-plugin-react).
It publishes `@vitejs/plugin-react` to GitHub Packages as `@pattobrien/plugin-react`, so Patt's
projects can depend on changes that upstream has not merged yet.

## What this fork carries

- Fast refresh and the React Compiler in named non-client environments, and no `react-dom` in
  `optimizeDeps` when it is not installed. Branch `feat-server-env-refresh`.
- The fork release workflow `.github/workflows/release-fork.yml` and its script
  `scripts/fork-publish.ts`.

Remove an item from this list once upstream merges it and a sync brings the change in.

## Package

`@vitejs/plugin-react` publishes as `@pattobrien/plugin-react` to `https://npm.pkg.github.com`.
The source `package.json` never changes. The release script rewrites the name at publish time and
restores the file after packing. Consumers alias it back:

```json
"@vitejs/plugin-react": "npm:@pattobrien/plugin-react@6.1.2-fork.1"
```

A fork tag is `plugin-react@<upstream>-fork.N`, and the published version is `<upstream>-fork.N`.
`<upstream>` is the `version` in `packages/plugin-react/package.json`. N increases with every fork
release and never reuses a number that has a tag.

## Cutting a release

1. To preview the tarball, run `node scripts/fork-publish.ts --dry-run <upstream>-fork.N` after
   `pnpm --filter @vitejs/plugin-react build`.
2. Tag the commit and push the tag:

   ```sh
   git tag plugin-react@<upstream>-fork.N <sha>
   git push fork plugin-react@<upstream>-fork.N
   ```

The tag push starts `release-fork.yml`, which builds and publishes. The script skips a version that
is already on the registry.

To publish an existing fork version again from `main`, run the workflow by hand:

```sh
gh workflow run release-fork.yml -R pattobrien/vite-plugin-react -f version=6.1.2-fork.1
```

## Syncing from upstream

1. Create a branch from `main`.
2. Merge upstream into the branch with `git fetch origin && git merge --no-ff origin/main`.
3. Open a PR into `main`.

Never rebase or force-push `main` or any pushed branch.
