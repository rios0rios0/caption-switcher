<h1 align="center">Caption Switcher</h1>
<p align="center">
    <a href="https://github.com/rios0rios0/caption-switcher/releases/latest">
        <img src="https://img.shields.io/github/release/rios0rios0/caption-switcher.svg?style=for-the-badge&logo=github" alt="Latest Release"/></a>
    <a href="https://github.com/rios0rios0/caption-switcher/blob/main/LICENSE">
        <img src="https://img.shields.io/github/license/rios0rios0/caption-switcher.svg?style=for-the-badge&logo=github" alt="License"/></a>
</p>

A lightweight Windows utility built entirely in Object Pascal using raw Win32 API calls (no VCL) that enumerates all visible top-level windows and allows the user to change their title bar text (caption) in real-time. The entire application is a single `.dpr` file with zero runtime library dependencies. This project was developed for educational purposes and is no longer actively maintained (discontinued 2013-03-28).

## Features

- **Window Enumeration** -- uses `EnumWindows` with a custom callback to discover all visible, top-level windows (excluding toolwindows and child windows), populating a sorted listbox
- **Caption Modification** -- changes any window's title bar text using `FindWindowA` and `SetWindowTextA`, applying the new caption immediately
- **Zero-VCL Architecture** -- the entire UI is built with raw Win32 API calls: `CreateWindowExA` for all controls (ListBox, Edit, Button, Static, GroupBox), `RegisterClassA` for the window class, and a manual `GetMessageA`/`TranslateMessage`/`DispatchMessageA` message loop
- **Custom Dark Theme** -- implements a dark color scheme through `WM_CTLCOLORLISTBOX`, `WM_CTLCOLORSTATIC`, and `WM_CTLCOLOREDIT` message handlers, setting white text on dark backgrounds with custom `HBRUSH` painting
- **Window Transparency** -- applies `WS_EX_LAYERED` with `SetLayeredWindowAttributes` for configurable alpha transparency on the main form
- **XP Visual Styles** -- includes a Windows XP application manifest (`XPManifest.Manifest`) embedded as a Win32 resource, enabling themed common controls via `comctl32.dll` version 6
- **Custom Icons** -- ships with multiple icon sizes (32x32, 64x64) embedded as resources, applied to the form via `WM_SETICON` and `SetClassLong`
- **Custom Font Rendering** -- creates a "Times New Roman" font via `CreateFontA` and applies it to all controls with `WM_SETFONT`
- **Centered Window Placement** -- calculates screen center position using `GetSystemMetrics(SM_CXSCREEN)` and `SM_CYSCREEN`
- **Refresh Capability** -- "Atualizar" button clears and re-enumerates all windows on demand
- **Click-to-Select** -- clicking a window name in the listbox automatically fills the "current caption" field via `LB_GETCURSEL` and `LB_GETTEXT`

## Technologies

| Component | Details |
|-----------|---------|
| **Language** | Object Pascal (Delphi 7) -- pure Win32 API, zero VCL |
| **APIs** | `Windows.pas`, `Messages.pas` (kernel32, user32, gdi32, comctl32) |
| **UI** | Raw `CreateWindowExA` controls: ListBox, Edit, Button, Static, GroupBox |
| **Theming** | XP Manifest (Common Controls 6.0) + custom `WM_CTLCOLOR*` dark theme |

## Project Structure

```
caption-switcher/
├── CS.dpr                        # Complete application source - window class, UI creation, message loop, window enumeration
├── XPManifest/
│   ├── XPManifest.Manifest       # Windows XP application manifest (Common Controls 6.0.0.0)
│   └── XPManifest.rc             # Resource compiler script for the manifest
├── ConIcon/
│   ├── ConIcon.rc                # Resource compiler script for icons (CONICON, ICO32, ICO64)
│   ├── Icon.ico                  # Default application icon
│   ├── Icon.png                  # Icon source image
│   ├── 32x32.ico                 # Small icon (taskbar, title bar)
│   └── 64x64.ico                 # Large icon (Alt+Tab, taskbar)
├── LICENSE                       # GNU General Public License v3.0
└── README.md
```

## UI Layout

The application window ("Caption Switcher v1.0", 346x324 pixels) is divided into two sections:

```
┌─ Janelas Detectadas (Detected Windows) ────────┐
│  ┌──────────────────────────────────────────┐   │
│  │  Sorted ListBox of visible windows       │   │
│  └──────────────────────────────────────────┘   │
│  [ Atualizar ]                                  │
└─────────────────────────────────────────────────┘
┌─ Titulo da Janela (Window Title) ───────────────┐
│  Atual:  [ current caption (read-only) ]        │
│  Novo:   [ new caption input             ]      │
│  [ Setar ]  [ Limpar ]                          │
└─────────────────────────────────────────────────┘
  Criado Por rios0rios0 ...
```

### Workflow

1. Click **Atualizar** to populate the list with all visible windows
2. Click a window name in the list -- its current caption fills the "Atual" field
3. Type the desired new caption in the "Novo" field
4. Click **Setar** to apply the change
5. Click **Limpar** to clear both fields

## Installation

1. Open `CS.dpr` in Borland Delphi 7 (or a compatible IDE)
2. Compile the project (Ctrl+F9)
3. Optionally, compile the resource files first: `brcc32 XPManifest\XPManifest.rc` and `brcc32 ConIcon\ConIcon.rc`

## Technical Details

### Window Enumeration Filter

The `EnumWindowsProc` callback filters windows using three criteria:

1. `IsWindowVisible(Wnd)` -- only visible windows
2. `GetWindowLong(Wnd, GWL_HWNDPARENT) = 0` or parent is the desktop -- only top-level windows
3. `(GetWindowLong(Wnd, GWL_EXSTYLE) and WS_EX_TOOLWINDOW) = 0` -- excludes tool windows

### Custom Color Painting

The application handles three color messages to achieve its dark theme:

- `WM_CTLCOLORLISTBOX` -- sets `$FFFFFF` text and black (`0`) background on the listbox
- `WM_CTLCOLORSTATIC` -- applies the same scheme to static labels and read-only edit controls
- `WM_CTLCOLOREDIT` -- applies dark styling to the editable text input

## Contributing

Contributions are welcome! Please open an issue or submit a pull request.

## License

This project is licensed under the [GNU General Public License v3.0](LICENSE).
