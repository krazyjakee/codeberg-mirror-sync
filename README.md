# codeberg-mirror-sync

Cloud-based one-way sync of GitHub repos → Codeberg, using **Codeberg pull-mirrors**.

## How it works

A scheduled GitHub Actions workflow ([`.github/workflows/mirror.yml`](.github/workflows/mirror.yml)):

1. Lists all **public, non-fork, non-archived** repos owned by `krazyjakee`.
2. For each repo missing on Codeberg, calls the Codeberg migration API to create
   a **pull-mirror** (`mirror: true`, interval `8h`).
3. For repos already mirrored, triggers an immediate `mirror-sync`.

After a mirror exists, **Codeberg itself** pulls from GitHub every ~8h with no
further help from this workflow. The workflow only provisions new mirrors and
nudges existing ones; new public repos auto-onboard on the next daily run.

Mirrors are **full** (all branches + tags) — Codeberg pull-mirrors cannot be
restricted to a single branch.

## Schedule

- Daily at `04:17 UTC` (cron).
- Manual via the **Run workflow** button (`workflow_dispatch`).

## Setup

The Codeberg token is stored as the repo secret **`CODEBERG_TOKEN`** and needs
the `write:repository` scope (to create mirrors). No GitHub token is configured
on Codeberg's side because only public repos are mirrored.

## Caveat

Per Codeberg's usage policy, Codeberg should not be used *purely* as a passive
backup/mirror of projects with no Codeberg engagement. Keep this for projects
you actually maintain a presence for on Codeberg.

## Direction

One-way, GitHub → Codeberg. Pushes made directly on Codeberg mirrors are
overwritten on the next pull.
