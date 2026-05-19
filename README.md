# codeberg-mirror-sync

Cloud-based one-way **push** mirror of GitHub repos → Codeberg.

> Codeberg disabled site-wide creation of *pull* mirrors, so syncing must be
> push-based: a plain Codeberg repo is created and pushed to from CI.

## How it works

Scheduled GitHub Actions workflow ([`.github/workflows/mirror.yml`](.github/workflows/mirror.yml)):

1. Lists **public, non-fork, non-archived** repos owned by `krazyjakee`, plus
   the explicitly pinned `EXTRA_REPOS` (`godot-mog`, `MoGen`,
   `DungeonTemplateLibrary-Godot`).
2. Ensures a plain Codeberg repo exists for each (creates via API if missing).
3. `git clone --mirror` from GitHub, then `git push --prune` of all
   `refs/heads/*` and `refs/tags/*` to Codeberg — a **full mirror** (all
   branches + tags; deletions propagate). PR refs are excluded.

New public repos auto-onboard on the next daily run.

## Schedule

- Daily at `04:17 UTC` (cron) and on-demand via **Run workflow**.

## Setup

- Repo is **public** so GitHub-hosted Actions minutes are free.
- Codeberg token stored as the encrypted repo secret **`CODEBERG_TOKEN`**
  (`write:repository` scope). No token is ever committed.

## Direction & caveats

One-way, GitHub → Codeberg. Anything pushed directly to a Codeberg mirror is
overwritten on the next run. Per Codeberg policy, don't use Codeberg purely as a
passive backup of projects with no Codeberg presence.
