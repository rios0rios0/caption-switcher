# Copilot Instructions

## Project Overview

**Caption Switcher** is a lightweight Windows utility built entirely in **Object Pascal (Delphi 7)** using raw **Win32 API** calls with **zero VCL dependency**. It enumerates all visible top-level windows and allows real-time modification of their title bar text (caption). The entire application is contained in a **single `.dpr` file** with no external runtime dependencies.

> **Status:** Discontinued March 28, 2013 — preserved as a historical reference. No new features or bug fixes are planned.

---

## Repository Structure

```
caption-switcher/
├── CS.dpr                        # Complete application source (~250 lines)
│                                 # Contains: window class, UI creation,
│                                 # message loop, window enumeration
├── XPManifest/
│   ├── XPManifest.Manifest       # Windows XP application manifest (Common Controls 6.0)
│   └── XPManifest.rc             # Resource compiler script for the manifest
├── ConIcon/
│   ├── ConIcon.rc                # Resource compiler script for icons
│   ├── Icon.ico                  # Default application icon
│   ├── Icon.png                  # Icon source image
│   ├── 32x32.ico                 # Small icon (taskbar, title bar)
│   └── 64x64.ico                 # Large icon (Alt+Tab, taskbar)
├── .github/
│   └── copilot-instructions.md  # This file
├── Clear.bat                     # Build artifact cleanup script
├── CONTRIBUTING.md               # Historical build information
├── LICENSE                       # GNU General Public License v3.0
└── README.md                     # Full documentation with UI layout
```

---

## Technology Stack

| Component       | Details                                                           |
|-----------------|-------------------------------------------------------------------|
| **Language**    | Object Pascal (Delphi 7 dialect)                                  |
| **Runtime**     | Win32 API — direct `kernel32`, `user32`, `gdi32`, `comctl32` calls |
| **UI Framework**| None — raw `CreateWindowExA` controls, manual message loop        |
| **Imports**     | `Windows.pas`, `Messages.pas`                                     |
| **Theming**     | XP Manifest (Common Controls 6.0) + custom `WM_CTLCOLOR*` dark theme |
| **Resources**   | Embedded icons (32x32, 64x64) + XP manifest via `brcc32`         |

**Zero external dependencies** — fully static, self-contained executable.

---

## Build Commands

There is **no automated build system** (no `Makefile`, no CI/CD). Building is done manually inside Borland Delphi 7.

### Steps (Historical)

1. Open `CS.dpr` in Borland Delphi 7 (or a compatible IDE)
2. *(Optional)* Compile resources first using the Borland Resource Compiler:
   ```bat
   brcc32 XPManifest\XPManifest.rc
   brcc32 ConIcon\ConIcon.rc
   ```
3. Compile and run: **F9** (compile + run) or **Ctrl+F9** (compile only)

### Cleanup

`Clear.bat` removes Delphi build artifacts (`.dcu`, `.opt`, `.dsm`, `.cfg`, `~*`).

---

## CI/CD Pipeline

**None.** There are no GitHub Actions workflows or any other CI/CD pipelines configured for this repository.

---

## Testing

**None.** There is no automated test suite. This is a historical single-file Win32 application with no testing infrastructure.

---

## Architecture & Design

### Single-File Monolithic Architecture

All application logic resides in `CS.dpr` (~250 lines of procedural Object Pascal). There are no modules, classes, or separation of concerns — typical of Delphi 7 Win32 API applications of this era.

### Key Procedures

| Procedure             | Purpose                                                             |
|-----------------------|---------------------------------------------------------------------|
| `WindowProc()`        | Main message loop handler (`WM_COMMAND`, `WM_CTLCOLOR*`, `WM_DESTROY`) |
| `EnumWindowsProc()`   | `EnumWindows` callback; filters visible top-level windows           |
| `CreateMyForm()`      | Creates the main window with layered transparency                   |
| `CreateMyComponent()` | Generic factory for Win32 controls (buttons, listbox, edits, labels) |
| `CreateMyFont()`      | Creates a Times New Roman font (size 14)                            |
| `GetText()`           | Reads window caption text                                           |
| `SetFormIcons()`      | Loads small/large icons from embedded resources                     |
| `XPManifest()`        | Initializes Common Controls 6.0 for XP visual styles               |

### Window Enumeration Filter

`EnumWindowsProc` applies three criteria:
1. `IsWindowVisible(Wnd)` — only visible windows
2. `GetWindowLong(Wnd, GWL_HWNDPARENT) = 0` — only top-level windows
3. `(GetWindowLong(Wnd, GWL_EXSTYLE) and WS_EX_TOOLWINDOW) = 0` — excludes toolwindows

### Custom Dark Theme

Implemented via Win32 color messages:
- `WM_CTLCOLORLISTBOX` — white text (`$FFFFFF`) on black background for the listbox
- `WM_CTLCOLORSTATIC` — same scheme for static labels and read-only edit controls
- `WM_CTLCOLOREDIT` — dark styling on the editable text input

### UI Layout

**Window:** "Caption Switcher v1.0", 346×324 pixels, centered on screen, with layered transparency (`WS_EX_LAYERED`).

```
┌─ Janelas Detectadas (Detected Windows) ─────────┐
│  ┌───────────────────────────────────────────┐   │
│  │  Sorted ListBox of all visible windows    │   │
│  └───────────────────────────────────────────┘   │
│  [ Atualizar (Refresh) ]                         │
└──────────────────────────────────────────────────┘
┌─ Titulo da Janela (Window Title) ────────────────┐
│  Atual:  [ current caption (read-only) ]         │
│  Novo:   [ new caption input           ]         │
│  [ Setar ]  [ Limpar ]                           │
└──────────────────────────────────────────────────┘
  Criado Por rios0rios0 ...
```

---

## Coding Conventions

### Hungarian Notation

- `h` prefix for window handles: `hFrm`, `hLst`, `hBtnSetar`, `hEdtNovo`, `hFont`
- Win32 type aliases used directly: `HWND`, `DWORD`, `TMsg`, `HBRUSH`

### Mixed-Language Identifiers

- UI labels and some variable names are in **Portuguese**: "Janelas Detectadas", "Atualizar", "Setar", "Limpar", `hEdtAtual`, `hEdtNovo`
- Core logic variables use English: `Result`, `Wnd`, `Line`

### Style

- Procedural code only — no OOP
- Global variables for all window handles (no encapsulation)
- Sparse comments; code is intended to be self-documenting
- 2-space indentation (inconsistent in places)
- No error handling beyond `try/except` in `EnumWindowsProc`

---

## Development Workflow

Since this is a **historical, discontinued project**, no active development workflow is defined. If making archival changes:

1. Edit `CS.dpr` (and optionally resource files) in a text editor or Delphi 7 IDE
2. Recompile using Borland Delphi 7 if a binary output is needed
3. Run `Clear.bat` to remove Delphi build artifacts before committing
4. Commit only source files — never commit `.exe`, `.dcu`, `.opt`, or other build outputs (see `.gitignore`)

---

## Common Tasks

### Viewing the Source

Open `CS.dpr` directly — it is the entire application in a single file.

### Cleaning Build Artifacts

```bat
Clear.bat
```

### Understanding Win32 APIs Used

Key Win32 functions referenced in `CS.dpr`:
- `EnumWindows` / `IsWindowVisible` / `GetWindowLong` — window discovery
- `FindWindowA` / `SetWindowTextA` — caption modification
- `CreateWindowExA` / `RegisterClassA` — UI construction
- `GetMessageA` / `TranslateMessage` / `DispatchMessageA` — message loop
- `SetLayeredWindowAttributes` — transparency
- `CreateFontA` / `WM_SETFONT` — font management
- `LoadIcon` / `WM_SETICON` / `SetClassLong` — icon management

---

## License

GNU General Public License v3.0 — see [LICENSE](../LICENSE).
