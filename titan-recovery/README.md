# Titan recovery bundle — 2026-11-08

Independent off-machine copy of the local Titan production history, published because
`iamlukethedev/Claw3D` denies push access to the `vincentpamenter-ai` account
(`permissions.push: false`), leaving the work otherwise stranded on one MacBook.

| Field | Value |
|---|---|
| Source HEAD | `bcbf60fa1387dbf8243049167cd525a55e2085ab` |
| Commits reachable | 681 |
| Files in tree | 889 |
| Bundle | `titan-production-full.bundle` |
| SHA256 | `d566aac2221a86a73f13e5bc02aa3f5245313133730b92d8cb8affc06091ffd2` |

## Restore

```bash
git clone titan-production-full.bundle titan-restored
cd titan-restored
git rev-parse HEAD          # expect bcbf60fa1387dbf8243049167cd525a55e2085ab
git rev-list --count HEAD   # expect 681
git ls-files | wc -l        # expect 889
npm ci && npx vitest run tests/unit
```

## Why the previous bundle was incomplete

The repository was a **shallow** clone (`.git/shallow` containing `0565b789`) combined with a
`blob:none` promisor filter. `git bundle create --all` therefore produced a file that
`git bundle verify` accepted but that could not be cloned: traversal hit the shallow boundary
and aborted with `Failed to traverse parents of commit 0565b789`.

The fix was `git fetch --unshallow --filter=blob:none origin`, which recovered the missing 66
commits (615 → 681). Only then did a bundle restore completely. This is why the restore test
here is a real `git clone` plus a commit count, not `git bundle verify`.

## Scope and limits

Contains source history only — no secrets, no tokens, no `.env` files. Scanned for credential
patterns and for both live operator tokens before publication; none present.

This is a recovery artifact, not a deployment. It does not claim the bundled code is installed
on any host.

---

# Update — Turn 12 final state (2026-11-09 00:20Z)

| Field | Value |
|---|---|
| Source HEAD | `8e904582ad2c42d512bee210b160beed1c064c14` |
| Commits reachable | 687 |
| Files in tree | 893 |
| Bundle | `titan-turn12-final.bundle` |
| SHA256 | `4aac0df16de95e3ec55086b52863e3393219e29d005efc092cec5941fe5b540c` |

Restore-verified by real clone: 687 commits, 893 files, Turn 12 work intact
(`src/lib/workforce/standingAuthority.ts` and the morning acceptance record both present).

The earlier `titan-production-full.bundle` (`bcbf60f`) is retained as the Turn 11 point.
This bundle supersedes it as the latest state.

---

# Update — Turn 13 final state (2026-11-09 01:15Z)

| Field | Value |
|---|---|
| Source HEAD | `9949310fc8fa0bf7101a5c937dbecc8762f63acd` |
| Commits reachable | 691 |
| Files in tree | 897 |
| Bundle | `titan-turn13.bundle` |
| SHA256 | `dfa23d0ecbe1eb91dd693c079b88015a17b833c027fc04aed5c9cf6c102a3250` |

Restore-verified by real clone: 691 commits, 897 files, the Core-status typed-state fix and the
presence roster fix both present.

Turn 13 fixed the ghost tower: sixteen roster roles existed but every floor rendered zero agents
because the presence resolver queried a dead endpoint; Core status was permanently UNKNOWN
because the badge required a field the route never sent; Follow Work was aborted by an 8s proxy
timeout below the measured 5-15.5s working duration. Published history now supersedes the Turn
12 bundle as the latest off-machine copy.
