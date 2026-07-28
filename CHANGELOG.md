# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- (placeholder for upcoming changes)

## [v0.1.0] - 2026-07-13

### Added
- Public split from `crab-incubator` — fresh repo export with no inherited history
- Developer brief (`docs/DEVELOPER_BRIEF.md`)
- Self-hosted CI runner (Anvil WSL)

### Changed
- Package version set to `0.1.0` for initial public release (pyproject.toml)

## [v1.0.0] - 2026-05-10

> **Note:** This version was released in the private `crab-incubator` repo prior to the public split on 2026-07-13. The public `crab` repo starts at `v0.1.0`.

### Added
- Portable CRAB Daemon (`crab_daemon.py`) — stdlib-only, zero dependencies
- 4 pluggable bus backends: TSV, JSONL, stdout, callback
- 3 built-in lanes: cleanup, git-audit, bus-audit
- Multi-lane work streams with independent schedules
- CRAB stop conditions (blockers, stashes, cooldowns)
- Dry-run mode for safe testing
- 18 unit tests, all passing
- Redteam security audit: 10/10 PASS
- Apache-2.0 license
