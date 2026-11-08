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
