# Developer Brief — crab & crab-incubator

**Date:** 2026-07-28
**Author:** devin
**Purpose:** Orient a new developer (human or agent) to both repos, their relationship, current state, and what to work on.

---

## TL;DR

There are **two CRAB repos** with swapped descriptions on GitHub (cosmetic bug, see below). They serve different roles in a public/private split:

| Repo | Role | Visibility | Status |
|------|------|-----------|--------|
| `hummbl-dev/crab` | **Public release** — portable CRAB protocol + daemon | Public | v1.0.0 released 2026-07-13, actively maintained |
| `hummbl-dev/crab-incubator` | **Private incubator** — full dev history, internal planning, bridge artifacts | Private | Stable, last commit 2026-07-19, no active work |

**CRAB** = "Coordination Receipts for Agent Behavior" — a 4-step protocol (CRAWL/Check → Reason → Act → Bus) for multi-agent coordination. Every autonomous turn reads live state, decides whether to act, performs the work, and posts a receipt to a coordination bus.

---

## The naming confusion (FIXED 2026-07-28)

The GitHub descriptions were **swapped** — now fixed:
- `crab` repo: "CRAB methodology: Check, Reason, Act, Bus for multi-agent coordination" ✓
- `crab-incubator` repo: "HUMMBL Crab - distributed worktree management for parallel agent execution (private incubator)" ✓

---

## Repo 1: `crab` (public release)

**URL:** https://github.com/hummbl-dev/crab
**Created:** 2026-07-13 (public split from incubator)
**Last commit:** 2026-07-27 (dependabot bump)
**License:** Apache-2.0
**Language:** Python 3.11+
**Runtime deps:** Zero (stdlib only)
**Package name:** `hummbl-crab` on PyPI path (v0.1.0 in pyproject.toml — note: pyproject says 0.1.0, CHANGELOG says v1.0.0; **version mismatch, needs reconciliation**)

### What's in it

| Path | Purpose | LOC |
|------|---------|-----|
| `crab_daemon.py` | Portable reference daemon — the core artifact | 970 |
| `crab_lane_optimizer.py` | Experimental lane tuning | 242 |
| `bridge_crab_fm.py` | HUMMBL/founder-mode bridge (**private, should not be public** — see REPO_BOUNDARIES.md) | 136 |
| `bus/bus_base.py` | Bus backend abstraction | 209 |
| `bus/crab_bus.py` | TSV/JSONL/stdout/callback bus implementations | 151 |
| `tests/` | 4 test files (test_crab_bus, test_daemon, test_lane_optimizer, test_terminal_rendering) | — |
| `docs/` | Methodology, implementation guide, adoption checklist, ADRs, branding | — |
| `examples/` | Usage examples | — |

**Total core code:** ~1,708 LOC

### Key features

- **4-step protocol:** CRAWL/Check → Reason → Act → Bus
- **4 pluggable bus backends:** TSV, JSONL, stdout, callback (shell command)
- **Multi-lane work streams:** Independent lanes with separate schedules and stop conditions
- **3 built-in lanes:** cleanup, git-audit, bus-audit
- **Stop conditions:** blockers, stashes, cooldowns
- **Dry-run mode** for safe testing
- **Container ready:** single file, drop into Docker/K8s/systemd

### CI status

- **Workflow:** `ci.yml` (lint + test gates)
- **Runner:** Self-hosted `Anvil` on Anvil WSL (currently offline — same flapping issue as hd-ai-adoption-prototype)
- **Last 5 runs:** all `success` (last run 2026-07-27, 2m40s)
- **Open PRs:** 0
- **Open issues:** 0

### Security posture

- Red team audit completed 2026-05-10: **10/10 PASS** (see `AUDIT.md`)
- SHA-pinned GitHub Actions (PR #2)
- Same-repo PR guard for self-hosted runners (PR #4)
- Dependabot enabled for github-actions and pip
- Public/private split audit completed 2026-07-13 — repo exported with no inherited history

### Known issues / TODOs

1. **`bridge_crab_fm.py` is in the public repo but should be private** — `docs/REPO_BOUNDARIES.md` explicitly lists it as "private/internal candidate." It contains HUMMBL/founder-mode bridge code with local infrastructure assumptions. **Action: move to incubator or internal namespace.**
2. **Version mismatch** — `pyproject.toml` says `0.1.0`, `CHANGELOG.md` says `v1.0.0`. Reconcile.
3. **Runner offline** — self-hosted runner `Anvil` is offline (same WSL systemd flapping issue affecting the fleet). CI will fail on next push.
4. **GitHub description is wrong** (see naming confusion above).

---

## Repo 2: `crab-incubator` (private incubator)

**URL:** https://github.com/hummbl-dev/crab-incubator
**Created:** 2026-05-08
**Last commit:** 2026-07-19 (CODEOWNERS added)
**License:** Apache-2.0
**Language:** Python 3.11+

### What it is

The private development repo where CRAB was built before being split out to the public `crab` repo on 2026-07-13. Contains:

- Full development history (pre-public-split)
- Internal planning documents (`PRODUCTIZATION.md` lives here conceptually — actually it's in `crab` but should be here)
- Bridge artifacts (`bridge_crab_fm.py` and related)
- Peer review records
- Research notes with citations
- HUMMBL-specific demos and governance maturity artifacts

### Current state

- **Stable, no active development.** Last commit 9 days ago (CODEOWNERS).
- **CI passing** (last run 2026-07-19, 18s — minimal workflow)
- **Runner:** `anvil-wsl-crab-incubator` (offline, same WSL issue)
- **Open PRs:** 0
- **Open issues:** 0
- **Not cloned locally** — only `crab` is at `/Users/others/PROJECTS/crab`

### Recent history (notable commits)

| Date | Commit | Description |
|------|--------|-------------|
| 2026-07-19 | `0c58925` | chore: add CODEOWNERS file |
| 2026-07-13 | `0bc978d` | docs: repo hygiene cleanup + security topology scrub (#23) |
| 2026-06-25 | `9f730fe` | fix(security): add allow-dirty to read-only audit lanes (B3) (#19) |
| 2026-06-25 | `209e432` | fix(security): close callback allowlist bypass |
| 2026-06-25 | `c083966` | fix(security): CRAB P0 fixes from Tier 1 cyber scan (CRAB-001/002/004) (#17) |
| 2026-06-22 | `bce7ca1` | feat(governance): adopt HUMMBL Repo Standard v0.1 (#16) |

### Relationship to `crab`

Per `docs/REPO_BOUNDARIES.md` (in the `crab` repo):
> "This is the public CRAB repository. It contains the portable CRAB protocol implementation, documentation, peer reviews, and branding artifacts. The private incubator (`hummbl-dev/crab-incubator`) retains the full development history with internal planning and bridge artifacts."

The public `crab` repo was **exported as a fresh repo with no inherited history** on 2026-07-13. It is not a fork — it's a clean export.

---

## How they relate to `founder-mode`

CRAB was extracted from `founder-mode` (the internal ops platform). The `PRODUCTIZATION.md` doc (currently in `crab` repo, should be in incubator) describes the full productization pipeline:

- **Tier 1 (done):** CRAB Protocol (public release), hummbl-governance (on PyPI)
- **Tier 1 (needs work):** Coordination Bus extraction as `crab-bus`
- **Tier 2 (moderate effort):** CRAB Dashboard, Agent Identity Registry, Cost Governor, Schema Validator, Briefing Engine
- **Tier 3 (long-term):** Autoresearch Pipeline, Trading Loop, Cognition Layer, Full Agent Fleet

`bridge_crab_fm.py` is the bridge that connects the portable CRAB daemon to the HUMMBL/founder-mode internal infrastructure. It is the coupling point that makes the public repo not fully portable.

---

## What to work on (prioritized)

### P0 — Fix the runner (blocking all CI)

Both repos have self-hosted runners on Anvil WSL. The runner was offline due to a Windows BSOD (bindflt.sys refcount underflow bug, 2026-07-28). The runner is back online and processing jobs. See the runner migration plan at `hd-ai-adoption-prototype/docs/runner-migration-plan-2026-07-28.md` for the full diagnosis.

### P1 — Move `bridge_crab_fm.py` out of the public repo (DONE 2026-07-28)

`bridge_crab_fm.py` has been removed from the public `crab` repo. It already exists in `crab-incubator`. References in README, AGENTS.md, AUDIT.md, REPO_BOUNDARIES.md, and BUS_ALIGNMENT_founder_mode.md have been updated. The bridge test in `test_daemon.py` has been skipped.

### P2 — Fix the GitHub description swap (DONE 2026-07-28)

Descriptions swapped via `gh repo edit`. See "The naming confusion" section above.

### P3 — Reconcile version mismatch (DONE 2026-07-28)

Added `v0.1.0` entry to CHANGELOG.md for the public split (2026-07-13). The `v1.0.0` entry is annotated as the pre-split incubator version. `pyproject.toml` remains at `0.1.0` (correct for initial public release).

### P4 — Move `PRODUCTIZATION.md` to incubator (DONE 2026-07-28)

`PRODUCTIZATION.md` has been removed from the public `crab` repo. It already exists in `crab-incubator`. References in README, UNIFIED_ROADMAP.md, and REPO_BOUNDARIES.md have been updated.

### P5 — Consider PyPI publication

`pyproject.toml` has the package name `hummbl-crab` but it's not on PyPI. If CRAB is meant to be a public product, publish it. Coordinate with `hummbl-governance` which is already on PyPI.

---

## Quick start for a new developer

```bash
# Clone the public repo
git clone https://github.com/hummbl-dev/crab.git && cd crab

# Setup
python -m venv .venv && source .venv/bin/activate
pip install -e ".[test]"

# Generate default config
python crab_daemon.py --init

# Run one turn
python crab_daemon.py --once --verbose

# Run tests
python -m pytest tests/ -v

# Run continuously
python crab_daemon.py --verbose
```

**Read order:**
1. `README.md` — overview + quick start
2. `docs/methodology.md` — the 4-step protocol
3. `docs/implementation-guide.md` — how to implement CRAB
4. `docs/REPO_BOUNDARIES.md` — what's public vs private
5. `AUDIT.md` — security audit results
6. `crab_daemon.py` — the core code
7. `PRODUCTIZATION.md` — future product roadmap (should be in incubator)

---

## Bus references

- This brief was requested 2026-07-28 by operator
- Related: runner migration plan at `hd-ai-adoption-prototype/docs/runner-migration-plan-2026-07-28.md`
- Productization context: `crab/PRODUCTIZATION.md` (internal)
