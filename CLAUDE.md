d# ClipMaster - GNOME Shell Extension

Clipboard manager extension. UUID: `clipmaster@gnome.extension`, version 1.3.1.

## Structure

```
extension.js          # Entry point: enable()/disable(), keyboard shortcuts, show/hide popup
prefs.js              # Extension preferences UI
stylesheet.css        # Popup styles
schemas/              # GSettings schema (org.gnome.shell.extensions.clipmaster)
src/
  Manager/
    ClipboardMonitor.js   # Watches Meta.Selection owner-changed signal; classifies clipboard content
    Database.js           # JSON flat-file storage (~/.local/share/clipmaster/clipboard.json)
  UI/
    Popup.js              # St.BoxLayout added to Main.layoutManager chrome
    Indicator.js          # PanelMenu.Button in system tray
  Util/
    Constants.js          # ItemType enum, debug logging
    Utils.js              # SignalManager, TimeoutManager, SettingsCache, HashUtils, etc.
    Encryption.js         # Simple XOR encryption for DB
    ImageStorage.js       # File-based image storage with thumbnails
    QrCodeGenerator.js    # QR code generation for URLs
```

## Key Behaviors

- **Monitoring**: listens to `Meta.Selection` `owner-changed` signal; checks MIME types for images first, then reads text
- **Classification**: text categorized as TEXT, URL, COLOR, HTML, FILE, or CODE (regex heuristics)
- **Storage**: debounced JSON saves (500ms); optional encryption; favorites exempt from pruning
- **Popup**: shown by adding to chrome (`Main.layoutManager.addChrome`); removed from chrome when hidden to avoid input blocking
- **Indicator**: left-click toggles popup; right-click shows quick-access menu; uses a dummy `menu.open()` trick for Dash to Panel auto-hide compatibility

## Keyboard Shortcuts (GSettings)

| Key | Default |
|-----|---------|
| `toggle-popup` | `Shift+Super+V` |
| `paste-as-plain` | `Ctrl+Shift+V` |

Change via: `gsettings set org.gnome.shell.extensions.clipmaster toggle-popup "['<Super>v']"`

## GNOME Extension Basics

- Run inside the GNOME Shell process (not a separate app)
- Use `gi://` imports for native libs (GLib, Gio, St, Clutter, Meta, etc.)
- `enable()` / `disable()` lifecycle — must clean up all signals/timeouts in `disable()`
- Stylesheet auto-loaded from root in GNOME Shell 45+


# Notes for the user
## How to run
0. run ./install.sh
1. Start a nested Wayland session:  dbus-run-session -- gnome-shell --nested --wayland

