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
