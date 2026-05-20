# Changelog

All notable changes to this project will be documented in this file.

The format is based on Keep a Changelog and this project follows Semantic Versioning.

## [Unreleased]

### Changed
- README now includes a one-line `curl` installer that installs to `/usr/local/bin/nblock`.
- `add/remove` now auto-bootstrap global wiring by default (no separate setup step).
- `auto-setup` now injects global include in `http {}` of `nginx.conf` (layout-agnostic).
- Added `--no-auto-setup` to skip bootstrap when already configured.

### Added
- `servers --server-file <path>` to list detected `server {}` blocks.
- `bind-server --server-file ...` with selectors (`--server-name`, `--listen`, `--index`) to bind include to exactly one server block safely.

## [0.1.0] - 2026-05-20

### Added
- Initial `nblock` utility.
- `add`, `remove`, `list`, and `auto-setup` commands.
- Optional `--reload` support (`nginx -t` + reload).
- Global include auto-bootstrap and optional `--no-auto-setup` override.
- IPv4 validation and duplicate rule protection.
- README with quick start and production notes.
