> **This is an unofficial fork of [joshmckinney/geforcenow-presence](https://github.com/joshmckinney/geforcenow-presence) with added support for Vesktop (the native RPM build).**
> All credit for the original project goes to [Josh McKinney](https://github.com/joshmckinney).
> See [Changes from upstream](#changes-from-upstream) for details.

---

<div align="center">
  <h1>🎮 GeForce NOW Rich Presence for Discord — Linux</h1>
  <p>
    <strong>Show your real game on Discord while playing on GeForce NOW — automatically, on Linux.</strong>
  </p>
  <p>
    A compiled Go binary that monitors the <a href="https://international.download.nvidia.com/GFNLinux/flatpak/geforcenow.flatpakrepo">GeForce NOW</a> Flatpak,
    detects the game you're playing via the window title, and updates your Discord Rich Presence
    with the correct game name, artwork, and elapsed time.
  </p>
  <p>
    <strong>✅ This fork works with Vesktop (native RPM) in addition to the official Discord client.</strong>
  </p>
</div>

---

## ✨ Features

- 🐧 **Native Linux support** — compiled Go binary, zero runtime dependencies
- 💬 **Vesktop support** — works with Vesktop (native RPM), Discord RPM/DEB, and Flatpak
- 🎮 **Automatic game detection** — reads the GeForce NOW window title via GNOME Shell extension D-Bus
- 🖼️ **Zero-Auth Metadata Pipeline** — queries public Steam and GOG APIs to push live HTTP box-art URLs to Discord natively without developer keys
- 🌟 **Native Discord Apps Tracking** — dynamically syncs with Discord's 22,000+ app database. Fuzzy matches game strings directly to official Client IDs to natively display the game on your profile.
- 🚀 **Auto-start support** — systemd user service for background operation on login (toggleable via UI)
- ⚙️ **Persistent Configuration** — Polling intervals, startup delays, and language settings are saved and easily editable via the System Tray.
- 🎨 **Custom Status Colors** — Personalize your tray icon's "Playing", "Idle", and "Error" states via a built-in color picker.
- 🛡️ **Optional Game History** — Capture Geforce NOW sessions in your Discord 30-day activity record via a specialized dummy-process launcher (disabled by default).
- 🔍 **Update Checker** — Automatic background checks for new releases on startup + manual check button.
- 📂 **Quick Access** — Open logs or configuration folders directly from the tray.
- 🧩 **Multi-compositor support** — GNOME (Wayland), X11 (xprop/xdotool), Hyprland, with fallback chain
- 🔒 **Single instance** — lock file prevents duplicate processes
- 🌐 **i18n** — locale detection with language files in `lang/`

---

## 📋 Requirements

| Requirement | Details |
|:---|:---|
| **OS** | Linux (tested on Fedora 44, should work on any distro with GNOME 45+) |
| **GeForce NOW** | [Official Nvidia Flatpak](https://international.download.nvidia.com/GFNLinux/flatpak/geforcenow.flatpakrepo) (`com.nvidia.geforcenow`) |
| **Discord client** | [Vesktop](https://github.com/Vencord/Vesktop) (native RPM), Discord native RPM/DEB, or Discord Flatpak |
| **Go** | 1.21+ (build only — not needed to run the binary) |
| **Build Dependencies** | `libayatana-appindicator3-dev` (Debian/Ubuntu) or `libayatana-appindicator-gtk3-devel` (Fedora/RHEL)<br>`libgtk-3-dev` (Debian/Ubuntu) or `gtk3-devel` (Fedora/RHEL) |
| **Desktop** | GNOME on Wayland (primary), X11, Hyprland (experimental) |

> **Note:** Running GeForce NOW via Chrome/Edge browser is **not currently supported** — only the official Flatpak app.

---

## 🚀 Quick Start (Fedora)

```bash
# 1. Install build dependencies
sudo dnf install -y golang gtk3-devel libayatana-appindicator-gtk3-devel

# 2. Clone this fork
git clone https://github.com/sdrcgh/geforcenow-presence-vesktop.git
cd geforcenow-presence

# 3. Build and install
make install

# 4. Log out and back in to activate the GNOME Shell extension, then:
make enable
```

---

## 📥 Installation

### 📦 Download a Pre-built Release (Easiest)

Go to the [Releases page](../../releases) of this fork and download the `.tar.gz` for your system. Extract it and run:

```bash
sudo dnf install -y golang gtk3-devel libayatana-appindicator-gtk3-devel
tar -xzf geforcenow-presence-vesktop.tar.gz
cd patched-src
make install
```

Then log out and back in, and run:

```bash
make enable
```

### 🛠️ Build from Source

```bash
# Install build dependencies (Fedora/RHEL)
sudo dnf install -y golang gtk3-devel libayatana-appindicator-gtk3-devel

# Clone this fork (not the upstream repo)
git clone https://github.com/sdrcgh/geforcenow-presence-vesktop.git
cd geforcenow-presence

# Build and install everything
make install
```

`make install` sets up everything needed for production use:

| Component | Location | Purpose |
|:---|:---|:---|
| **Binary** | `~/.local/bin/geforcenow-presence` | The compiled Go executable |
| **Config** | `~/.config/geforcenow-presence/` | `app_settings.json` and language files |
| **Logs/State** | `~/.local/state/geforcenow-presence/` | `geforce_presence.log` and cached data |
| **GNOME Extension** | `~/.local/share/gnome-shell/extensions/window-title-server@geforcenow-presence/` | Reads Wayland-native window titles via D-Bus |
| **Systemd Service** | `~/.config/systemd/user/geforcenow-presence.service` | Runs the binary as a background daemon on login |
| **Desktop Entry** | `~/.local/share/applications/geforcenow-presence.desktop` | Shows in application launcher |

### First-Time Setup

On GNOME Wayland, the GNOME Shell extension needs a session restart to load:

1. Run `make install`
2. **Log out and back in** (or restart GNOME Shell)
3. Run `make enable`
4. Launch a game on GeForce NOW — your Discord/Vesktop status will update automatically

---

## 🗑️ Uninstalling

```bash
make uninstall
```

This cleanly removes:
- ✅ Stops and disables the systemd service
- ✅ Removes the binary from `~/.local/bin/`
- ✅ Removes the systemd service file
- ✅ Removes the desktop entry
- ✅ Removes the GNOME Shell extension
- ✅ Reloads systemd daemon

Config files at `~/.config/geforcenow-presence/` are **preserved**. To remove those too:

```bash
rm -rf ~/.config/geforcenow-presence
```

---

## ⚙️ Usage

```bash
# Run in foreground (useful for debugging)
./geforcenow-presence

# With options (CLI flags override persistent settings)
./geforcenow-presence --delay 10    # wait 10s before starting
./geforcenow-presence --interval 5  # poll every 5 seconds

# Service management
make status     # check service status
make restart    # restart the service
make disable    # stop and disable auto-start
```

The system tray icon provides access to polling interval, startup delay, auto-start toggle, language switcher, custom status colors, update checker, and config folder.

---

## 📚 Documentation

- [**Architecture & Design**](docs/ARCHITECTURE.md) — D-Bus flow, Zero-Auth metadata pipeline, and Discord IPC mechanisms.
- [**Building and Releasing**](docs/BUILDING_AND_RELEASING.md) — Compiling from source, `.deb`/`.rpm` packages.
- [**Extending & Modifying**](docs/CONTRIBUTING.md) — How to add compositors, browser support, and custom game overrides.

---

## 🤝 Support & Contributing

For issues specific to this fork (Vesktop support), please open an issue here.

For issues with the underlying tool, see the [upstream repository](https://github.com/joshmckinney/geforcenow-presence).

---

## Changes from upstream

### Vesktop Support

The original project only detects the official Discord RPM as a running Discord client. This fork adds support for **[Vesktop](https://github.com/Vencord/Vesktop)** (native RPM install), which uses its own binary named `vesktop`.

**Files changed:**

- `internal/launcher/launcher.go` — added `IsDiscordRunning()` which checks for `discord`, `vesktop`, and `equibop` process names; updated `LaunchDiscord()` to also look for Vesktop at `/usr/bin/vesktop`
- `internal/presence/presence.go` — replaced the hardcoded `IsProcessRunning("Discord")` check with the new `IsDiscordRunning()`
- `internal/discord/rpc.go` — added the Vesktop Flatpak IPC socket path as an additional search location

**Why it was broken:** Vesktop creates the Discord IPC socket at the exact same standard path (`$XDG_RUNTIME_DIR/discord-ipc-0`) as the official Discord RPM — so the IPC side was never the problem. The tool was simply checking whether a process named `Discord` was running, finding none, and giving up before even attempting to connect.

Tested on **Fedora 44 Workstation** with Vesktop native RPM.

---

## 📜 License & Credits

This project is licensed under the **MIT License** — see [LICENSE](LICENSE) for details.

Original project by [Josh McKinney](https://github.com/joshmckinney), itself a Linux rewrite of [GeForce NOW Rich Presence](https://github.com/KarmaDevz/GeForce-NOW-Rich-Presence) by [KarmaDevz](https://github.com/KarmaDevz).

---

## ⚠️ Disclaimer

This software is **not affiliated with** NVIDIA, GeForce NOW, Discord, or Valve. All product names and logos are property of their respective owners. This application interacts with Discord's Rich Presence IPC API — use at your own discretion. See [LICENSE](LICENSE) for full terms.
