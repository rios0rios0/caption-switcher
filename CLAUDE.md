# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Single-file Windows utility that lists visible top-level windows and rewrites their title bar text. Written in Object Pascal (Delphi 7) against the raw Win32 API — zero VCL, zero runtime dependencies. Discontinued 2013-03-28; preserved as a historical reference, no active development.

The entire program is `CS.dpr` (~250 lines, procedural). `README.md` documents features and UI layout; `.github/copilot-instructions.md` mirrors this guidance for GitHub Copilot.

## Build

No Makefile, no local test/lint tooling — building happens inside Borland Delphi 7.

- Resources must be compiled to `.res` before the `.dpr` will link: `brcc32 XPManifest\XPManifest.rc` and `brcc32 ConIcon\ConIcon.rc`. The `{$R}` directives at the top of `CS.dpr` reference the resulting `.res` files, not the `.rc`.
- Compile/run in the IDE with F9 (or Ctrl+F9 to compile only).
- `Clear.bat` deletes Delphi build artifacts (`.dcu`, `.opt`, `.dof`, `.cfg`, etc.). Never commit build outputs — `.gitignore` covers them.

CI lives in `.github/workflows/` and only calls reusable `rios0rios0/pipelines` workflows — `release.yaml` runs the release on push to `main`; `claude-review.yaml` and `claude-mention.yaml` drive Claude's PR review and `@claude` responder. None of them build the Delphi source.

## Architecture invariants

- All window handles are module-level globals (`hFrm`, `hLst`, `hEdt*`, ...). There is no encapsulation; procedures read/write these directly.
- `WindowProc` dispatches on `uMsg`: `WM_COMMAND` compares `lParam` against button/listbox handles to route actions, `WM_CTLCOLOR*` paints the dark theme, `WM_DESTROY` calls `Halt` for a hard exit (not just `PostQuitMessage`).
- Caption changes go through `FindWindowA(nil, <current title>)` then `SetWindowTextA` — matching is by exact title string, so the "Atual" field must equal the live caption. Selecting a listbox row copies the caption into that field.
- The XP visual-styles init (`XPManifest`) resolves `InitCommonControls` at runtime and calls it via an inline `asm` block; leave the assembly intact.
- `WM_CTLCOLOR*` handlers return the shared `MyBrush` handle as the message result — this brush is created once and reused, not per-message.

## Conventions specific to this repo

- Hungarian handle prefixes: `h` for handles (`hFrm`, `hBtnSetar`, `hEdtNovo`).
- Identifiers and UI strings mix Portuguese (`Setar`, `Limpar`, `Atualizar`, `hEdtAtual`) with English (`Result`, `Wnd`, `Line`). Match the surrounding language when editing.
- ANSI (`*A`) Win32 calls throughout (`CreateWindowExA`, `SetWindowTextA`); message-box literals are Latin-1 accented Portuguese — preserve the encoding.
- Font size is passed negative (`-14`) — Win32 character-height convention, not a typo.

<!-- chlog:start -->
## Changelog (chlog) — MANDATORY

If the repository you are working in uses chlog (a `.chlog.yaml` or `.chlog.yml`
config file, or a `.changes/` directory, exists at the project root), the
following is binding and ALWAYS applies: whenever you make ANY change, you MUST
create a changelog fragment as part of the same change — automatically, without
being asked, before committing.

- Do NOT edit CHANGELOG.md directly; it is generated from fragments.
- Create the fragment with:
  `chlog new --kind <Kind> --body "<imperative description>"`
- Valid kinds: Added, Changed, Deprecated, Removed, Fixed, Security
- Choose the kind that best matches the change (e.g., new feature → Added,
  bug fix → Fixed, behavior change → Changed, removal → Removed, security fix → Security).
- If the change is backward-INCOMPATIBLE with the public API (a breaking
  change), you MUST add the `--breaking` flag:
  `chlog new --kind <Kind> --breaking --body "<description>"`.
  This is the ONLY thing that triggers a major version bump — the kind alone
  never does (per SemVer, major = incompatible change). When unsure whether a
  change breaks compatibility, ask the user instead of guessing.
- Fragments are YAML files in `.changes/unreleased/`; stage them with your commit.
- `chlog check` fails the build when a fragment is missing — never skip it.
<!-- chlog:end -->
