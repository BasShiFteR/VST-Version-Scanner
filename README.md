# VST Version Scanner

A Windows desktop utility that recursively scans one or more folders for `.dll` and `.vst3` plugin files and displays their version info, file size, product names and paths. Uses a 3-level version detection chain to resolve versions that most tools miss.

![Platform](https://img.shields.io/badge/platform-Windows%2010%2F11-blue)
![Version](https://img.shields.io/badge/version-1.0.5-orange)

---

## How to use

1. Launch `vst.exe`
2. The **Folders** field shows the current scan path (default: `C:\Program Files\Common Files\VST3`)
3. Click **Add...** to add a folder via dialog — you can add multiple folders before scanning
4. Hover over or click the Folders field to see all added paths in a tooltip
5. Click **Clear** to reset back to the default single folder
6. Enable **Deep scan** checkbox to activate VST3 API probing — resolves additional versions, identifies 32-bit plugins, and replaces all remaining `N/A` entries with a final result
7. Click **Scan** — the app walks all subfolders recursively for every added folder
8. Results appear in the table: **File | Version | Size | Product | Path**
9. Click any column header to sort — click again to reverse order (arrow shows direction); Size column sorts numerically
10. Type in the **Filter** field to instantly filter results across all columns (case-insensitive)
11. Right-click any row for a context menu: **Open folder in Explorer** or **Copy path**
12. **Export CSV** — saves a UTF-8 CSV file, opens correctly in Excel
13. **Export TXT** — saves a plain-text table with aligned columns, a timestamp, and respects current sort order

The last used folder list is saved automatically to `vst_config.json` next to the executable and restored on next launch — do not delete this file, otherwise all folder paths will need to be re-entered.

## Version detection — how it works

The scanner uses a 3-level fallback chain per plugin:

| Level | Method | What it reads |
|---|---|---|
| 1 | `moduleinfo.json` | VST3 bundle manifest (SDK 3.7+) |
| 2 | `Info.plist` | Older bundle XML metadata |
| 3 | VST3 API (Deep scan) | `IPluginFactory2::getClassInfo2()` via live DLL load in isolated subprocess |
| fallback | PE resource | Windows `VS_VERSION_INFO` via win32api |

Deep scan loads each plugin DLL in a separate subprocess with a 4-second timeout — if a plugin crashes on load, the main app is unaffected. It is also the only method that identifies 32-bit plugins. A progress bar and Stop button appear during deep scan.

## Download

The executable is fully portable — no installation required. All dependencies are bundled inside the `.exe`. Just download and run.

## Notes

- Deep scan is recommended for complete results, but it is entirely optional — use it based on how thorough you want the scan to be
- Without Deep scan, plugins that have no version in their bundle manifest or PE resources will show `N/A`
- After Deep scan, plugins show one of: a version string, `32-bit` (plugin cannot be loaded into a 64-bit process — version is not determined), or `No Info` (plugin exposes no version data through any available method)
- Deep scan can take a while on large collections — progress bar and count are shown in the status strip
- TXT export uses dynamic column widths — use a monospace font viewer for correct alignment (Notepad++, Total Commander Lister with word-wrap off)
- CSV export uses `;` delimiter (compatible with Czech/Slovak Excel locale)
- The **Size** column shows only the size of the plugin file or bundle directory itself — any external data (sample libraries, presets, etc.) stored elsewhere on disk is not included and can be significantly larger
- Added folders that no longer exist on disk are silently skipped when loading saved config
- If the app crashes, a `vst_error.log` file is created next to the executable with a full error report — attach it when reporting issues

## Author

Radek / BasShiFteR

For support or bug reports: [support@bsfr.dev](mailto:support@bsfr.dev)

[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/F0Y8219YJ2)
