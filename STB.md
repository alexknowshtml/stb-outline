# STB fork of Outline

This fork runs the Stacking the Bricks wiki at https://wiki.stackingthebricks.com.

## Branches

- `stb` (default): our changes. Started from upstream `v1.10.1`, the version running in production.
- `main`: an untouched mirror of `outline/outline`. Use GitHub's **Sync fork** button to update it, then merge a release tag into `stb`.

Keep `main` clean. Do all STB work on `stb` or on feature branches that merge into it.

## Local setup

Needs Node 26 (see `.nvmrc`), Yarn and Docker.

```bash
git clone git@github.com:alexknowshtml/stb-outline.git
cd stb-outline
cp .env.sample .env    # fill in the required values; see docs/ for details
make up                # starts Postgres + Redis in Docker, then the dev server
```

Upstream's guide lives in `docs/` and at https://docs.getoutline.com.

## Taking an upstream update

```bash
git remote add upstream https://github.com/outline/outline.git   # once
git fetch upstream --tags
git checkout stb && git merge v1.x.y    # the release tag to adopt
```

Keep changes small and localized to make these merges easy.

## Deploying

Push to `stb`, then ask Andy in Discord to deploy the STB wiki. Andy builds an image from `stb` and swaps it in on the server. Production secrets live only on the server and never go in this repo.

## For coding agents

These rules override upstream `CLAUDE.md` / `AGENTS.md` where they conflict.

**Fork rules**
- Work on `stb` or a feature branch off it. Never commit to `main`; it must stay identical to upstream so Sync fork keeps working.
- Every upstream update gets merged by hand, so keep the diff small. Change the fewest lines possible in upstream files. Prefer new files, or a single-line hook into an existing one, over rewriting upstream code.
- Upstream says not to create markdown files. `STB.md` is the exception. Add STB notes here, not in `CLAUDE.md` / `AGENTS.md`.
- Plugins (`plugins/`) cannot change the editor UI. Browser-side plugin hooks are limited to Settings, Imports and Icon. Editor changes must go in core code.

**Running it locally**
- `.env`: copy `.env.sample`. Set `SECRET_KEY` and `UTILS_SECRET` with `openssl rand -hex 32`. `.env.development` supplies the dev URL (`https://local.outline.dev:3000`), database and Redis values.
- Sign-in without SMTP: leave `SMTP_USERNAME` unset. In development, Outline sends mail to a throwaway ethereal.email inbox and logs `Preview Url: ...` in the backend output. Open that link to get the magic sign-in link.
- First run: the first account created at the local URL becomes admin.
- Checks before pushing: `yarn lint`, `yarn test`.

**Current goals (from Amy)**
The point of all three is to keep editor chrome from overlapping or crowding the text being read.
1. Move the block controls farther from the text. `.block-menu-trigger` in `shared/editor/components/Styles.ts` is positioned with `margin-left: -28px`.
2. Keep the floating selection toolbar from covering the selection. In `app/editor/components/FloatingToolbar.tsx`, the position calculation uses `selectionBounds.top - menuHeight` with no gap. Add a gap, and place the toolbar below the selection when there's no room above.
3. Add a permanent formatting toolbar on desktop. `app/editor/components/SelectionToolbar.tsx` already renders the toolbar as a fixed bottom bar on mobile while editing (`isMobileEditing`). Reusing that path on desktop is likely the smallest change. The bar must not cover document text either.

**Production**
The live wiki runs v1.10.1 on Andy's server behind Caddy, with Postgres 16 and Redis 7 in containers. Don't commit production config or secrets. Deploys go through Andy.
