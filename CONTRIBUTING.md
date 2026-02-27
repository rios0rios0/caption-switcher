# Contributing

> **This project was discontinued in March 2013 and is no longer actively maintained.**
> The repository is preserved as a historical reference. No new features or bug fixes are planned.

## Historical Build Information

This project was built using the following tools and technologies:

- **Language:** Object Pascal (Delphi 7) — pure Win32 API, zero VCL
- **IDE:** Borland Delphi 7
- **APIs:** `Windows.pas`, `Messages.pas` (kernel32, user32, gdi32, comctl32)
- **UI:** Raw `CreateWindowExA` controls (ListBox, Edit, Button, Static, GroupBox)
- **Theming:** XP Manifest (Common Controls 6.0) with custom `WM_CTLCOLOR*` dark theme

### Build Steps (Historical)

1. Open `CS.dpr` in Borland Delphi 7 (or compatible IDE)
2. Optionally compile resource files first: `brcc32 XPManifest\XPManifest.rc` and `brcc32 ConIcon\ConIcon.rc`
3. Compile and run (`F9`)

> **Note:** The entire application is a single `.dpr` file with zero runtime library dependencies. `Clear.bat` cleans build artifacts.
