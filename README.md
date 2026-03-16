<p align="center">
  <img src="https://git.rdenadai.dev/rdenadai/kenaz/raw/branch/main/static/logo_back.png" alt="Kenaz Logo" width="128" />
</p>

# Kenaz

Kenaz is a Linux-first browser shell written in Rust with Slint, WRY, and SQLite.
It aims for a familiar single-window browser experience with a native desktop chrome, real tabs, an omnibox, persisted browser data, and browser-like behavior where the platform webview already provides it.

Current version: `0.0.1`

## Quick start

### 1) Prerequisites

- Rust stable toolchain (install with [rustup](https://rustup.rs/))
- Linux desktop environment with GTK 3 / WebKitGTK support

Debian/Ubuntu packages:

```bash
sudo apt install libgtk-3-dev libwebkit2gtk-4.1-dev
```

If those development packages are not available system-wide, Kenaz can fall back to a repo-local sysroot under `.local/linux-sysroot` through the checked-in pkg-config wrapper. The first build may take longer while that cache is prepared.

### 2) Build and run

```bash
cargo run -p kenaz-app
```

Optimized local build:

```bash
cargo build -p kenaz-app --release
```

The release binary is written to `target/release/kenaz-app`.

### 3) Install locally on Linux (optional)

```bash
cargo build -p kenaz-app --release
./installer.sh install
```

To update an existing local install:

```bash
cargo build -p kenaz-app --release
./installer.sh update
```

The installer copies:

- the release binary to `~/.local/bin/kenaz`
- the launcher icon to `~/.local/share/icons/hicolor/256x256/apps/kenaz.png`
- a desktop entry to `~/.local/share/applications/kenaz.desktop`

If your desktop keeps an older cached launcher icon, run `./installer.sh update` again to rewrite the local desktop entry and icon path.

Useful development commands:

```bash
cargo fmt --all
cargo check --workspace
cargo test --workspace
```

## What you can do in Kenaz today

- Browse in a single native window with a tab strip, close buttons, and a new-tab/home surface.
- Type either URLs or search queries into the omnibox.
- Use back, forward, reload, and stop controls.
- Open `window.open`, Ctrl-click, and middle-click page links in new tabs.
- Keep bookmarks, history, downloads, homepage, search engine, theme, and download directory persisted locally.
- Use dedicated browser surfaces for bookmarks, history, downloads, settings, startup, and error/empty states.
- Use browser-style edit shortcuts across both the omnibox and embedded pages.
- Keep embedded page context menus native through WebKitGTK/WRY while exposing a Kenaz-specific right-click edit menu for the omnibox.

## Current keyboard shortcuts

| Shortcut | Action |
| --- | --- |
| `Ctrl+L` | Focus the omnibox |
| `Ctrl+X` | Cut in the omnibox, or send cut to the active page |
| `Ctrl+C` | Copy in the omnibox, or send copy to the active page |
| `Ctrl+V` | Paste into the omnibox, or send paste to the active page |
| `Ctrl+A` | Select all in the omnibox, or send select-all to the active page |
| `Ctrl+T` | Open a new tab |
| `Ctrl+W` | Close the active tab |
| `Ctrl+Shift+T` | Reopen the most recently closed tab |
| `Ctrl+D` | Bookmark the current page |
| `Ctrl+H` | Open History |
| `Ctrl+J` | Open Downloads |
| `Ctrl+,` | Open Settings |
| `Ctrl+Tab` | Switch to the next tab |
| `Ctrl+Shift+Tab` | Switch to the previous tab |
| `Ctrl+R` | Reload or stop the active page |
| `Alt+Left` | Go back |
| `Alt+Right` | Go forward |
| `Esc` | Close the open shell menu, omnibox suggestions, or omnibox context menu |

## Platform and runtime notes

- Kenaz currently targets Linux first, with X11 as the primary embedded-webview target for `v0.0.1`.
- Wayland is not the primary child-webview target yet; when running from a Wayland session today, XWayland is the practical path.
- Browser state is stored in SQLite at `$XDG_DATA_HOME/kenaz/kenaz.sqlite3`.
- If `XDG_DATA_HOME` is unset, Kenaz falls back to `$HOME/.local/share/kenaz/kenaz.sqlite3`.
- If neither environment variable is available, Kenaz falls back to `./.kenaz-data/kenaz.sqlite3`.
- The Settings surface can open a GTK folder chooser to select a custom download directory.
- Completed-download open/reveal actions use `xdg-open`, so the host desktop should provide a compatible opener.
- Kenaz includes a local Linux installer script (`./installer.sh`) but does not ship distro-specific packages yet.

## Current v1 scope

Kenaz's current `v0.0.1` scope is centered on the browser shell basics:

- single-window browser chrome
- multi-tab navigation
- bookmarks, history, downloads, and settings surfaces
- persistent browser settings and data
- Linux-first runtime support

The main follow-up work after this slice is moving the remaining blocking store and file operations out of direct UI callbacks and behind a background boundary.

## Workspace layout

```text
crates/
  kenaz-app/         binary entrypoint and startup wiring
  kenaz-ui/          Slint components, models, and UI adapter
  kenaz-core/        browser state, omnibox logic, themes, and routing
  kenaz-engine/      shared engine traits and event contracts
  kenaz-engine-wry/  WRY-backed browser engine
  kenaz-store/       SQLite persistence and migrations
static/
  logo.png
  logo_back.png
scripts/
  pkg-config-wrapper.py
```

## License

MIT OR Apache-2.0
