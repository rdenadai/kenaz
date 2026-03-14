# Kenaz

Kenaz is an open source browser project focused on building a simple, modern browser shell without platform lock-in.

## Stack

- Rust
- Slint for the desktop UI
- WRY for embedded web content
- SQLite for local data

## Development target

Kenaz v1 is being developed for Linux first, with X11 as the main target for the embedded browser shell.

Wayland is not the primary child-webview target yet. If you run Kenaz from a Wayland session today, treat XWayland compatibility as the practical path until the Linux shell is more stable.

## Status

Kenaz is in early development and now has a multi-tab WRY-backed shell with omnibox navigation, bookmarks/history/downloads/settings surfaces, browser-style edit shortcuts, and native-like open-in-new-tab plus context-menu behavior.

## Linux build dependencies

Kenaz currently targets Linux/X11 and needs the native GTK/WebKitGTK development packages that WRY expects:

```bash
sudo apt install libgtk-3-dev libwebkit2gtk-4.1-dev
```

If those dev packages are not available system-wide, Cargo now falls back to a repo-local sysroot under `.local/linux-sysroot` by using `apt download`, `apt-cache`, and `dpkg-deb` through the checked-in pkg-config wrapper. The first build may take longer while that local sysroot is prepared.

## Linux runtime notes

- Browser state is stored in SQLite at `$XDG_DATA_HOME/kenaz/kenaz.sqlite3`.
- If `XDG_DATA_HOME` is unset, Kenaz falls back to `$HOME/.local/share/kenaz/kenaz.sqlite3`.
- If neither environment variable is available, Kenaz falls back to `./.kenaz-data/kenaz.sqlite3`.
- The Settings surface can open a GTK folder chooser to save future downloads to a custom directory.
- Completed-download open/reveal actions use `xdg-open`, so the host desktop should provide a compatible opener.
- Embedded page context menus stay platform-native through WebKitGTK/WRY, while the omnibox exposes Kenaz's own right-click edit menu for cut/copy/paste/select-all.
- Kenaz currently assumes Linux/X11-style native dependencies and has not adopted distro-specific packaging metadata yet.

## Run

```bash
cargo run -p kenaz-app
```

For an optimized local build:

```bash
cargo build -p kenaz-app --release
```

The resulting binary is written to `target/release/kenaz-app`.
