# Repo layout — pointsav-fleet-deployment

Authoritative list of allowed top-level paths in this repository.
Files outside this allowlist must move; the rule is never loosened
to accommodate misplaced files. See
`~/Foundry/conventions/root-files-discipline.md` for the canonical
companion-file tier.

## Allowed root files

| File | Purpose | Required? |
|---|---|---|
| `README.md` | Public-facing English entry point | required |
| `INVENTORY.yaml` | Deployment inventory manifest | required |
| `LICENSE` | Repository license; canonical text from `factory-release-engineering/licenses/` | required |
| `SECURITY.md` | Security disclosure policy | required for public repos |
| `TRADEMARK.md` | Trademark policy (when distinct from LICENSE) | required for branded repos |
| `CLAUDE.md` | Repo-specific Claude Code guidance; ≤150 lines per CLAUDE.md size discipline | required |
| `NEXT.md` | Repo-scope hot items; ≤200 lines | optional |
| `CHANGELOG.md` | Version history per CLAUDE.md §7 | required for versioned repos |

## Allowed root directories

| Directory | Purpose |
|---|---|
| `.git/` | Git internal |
| `.agent/` | Engine-agnostic agent state (canonical; `.claude/` is a backward-compat symlink per AGENT.md) |
| `.github/` | GitHub CI / templates |

*Add per-repo content directories below this line.*

| Directory | Purpose |
|---|---|
| `media-knowledge-distribution/` | Newsroom and media distribution deployment node |
| `media-knowledge-documentation/` | Knowledge documentation deployment node |
| `media-marketing-landing/` | Marketing landing page deployment node |
| `vault-privategit-design-system/` | PointSav design-system private git deployment node |
| `vault-privategit-source/` | PointSav source control deployment node |

## Misplacement procedure

Files found outside this allowlist:
1. Surface in `cleanup-log.md` (`.agent/rules/cleanup-log.md`)
2. Move via `git mv` to the correct location (or to `~/Foundry/`
   workspace root if cross-repo)
3. Reference the move in commit message
4. Do NOT loosen this allowlist to accommodate

## Schema

This file follows `foundry-repo-layout-v1` (informal). The cluster
manifest at `<cluster>/.agent/manifest.md` may impose tighter
constraints; this file's allowlist is the floor.
