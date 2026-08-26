# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

This file is not edited by hand. Every change writes its own fragment under
`.changes/unreleased/` with [chlog](https://github.com/luizjhonata/chlog), and a release compiles
the pending fragments into a version section here — so two branches each adding an entry no
longer touch the same lines, and a rebase that used to conflict on this file now conflicts on
nothing.

When a new release is proposed:

1. Create a new branch `bump/x.x.x` (this isn't a long-lived branch!!!);
2. The fragments pending under `.changes/unreleased/` are compiled into a version section by `chlog batch auto && chlog merge` (AutoBump does this for you — it reads the fragments directly);
3. Open a Pull Request with the bump version changes targeting the `main` branch;
4. When the Pull Request is merged, a new Git tag must be created using <LINK TO THE PLATFORM TO OPEN THE PULL REQUEST>.

Releases to productive environments should run from a tagged version.
Exceptions are acceptable depending on the circumstances (critical bug fixes that can be cherry-picked, etc.).

## [Unreleased]

## [1.1.1] - 2026-08-11

### Changed

- refreshed `.github/copilot-instructions.md` to correct the build section, which wrongly claimed there was no CI/CD while the file's own CI/CD section documents `release.yaml`

## [1.1.0] - 2026-08-04

### Added

- created `CLAUDE.md` to document the Delphi 7 build steps, single-file architecture, and repo-specific conventions for Claude Code

## [1.0.3] - 2026-05-25

### Changed

- refreshed `.github/copilot-instructions.md` to add missing `CreateMyClass()` procedure and fix incomplete `EnumWindowsProc` filter description

## [1.0.2] - 2026-05-08

### Changed

- refreshed `.github/copilot-instructions.md` to document the `release.yaml` workflow and add missing files to the repository structure tree

## [1.0.1] - 2026-04-28

### Changed

- refreshed `.github/copilot-instructions.md` to include `CHANGELOG.md` in the repository structure tree

## [1.0.0] - 2019-01-02

The changes weren't tracked until this version.
