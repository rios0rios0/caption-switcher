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

CI (`.github/workflows/release.yaml`) only calls the reusable `rios0rios0/pipelines` release workflow on push to `main`; it does not build the Delphi source.

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
