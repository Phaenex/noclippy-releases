# NoClippy troubleshooting

Quick index for streamers. In-app: **Settings → Help** (recovery tools and copy-report).

## App still invisible in OBS after NoClippy is off or closed

Windows **capture-exclude** flags (`SetWindowDisplayAffinity`) can outlive NoClippy if the app crashes, is killed from Task Manager, or cleanup did not finish. The window stays on your monitor but vanishes from OBS until the flag is cleared.

**While NoClippy is running**

1. Turn **protection off** on the dashboard.
2. Click **Fix stuck windows** on the main screen. If that doesn't clear it, open **Help → Troubleshoot and recover** and choose **An app is still hidden when it shouldn't be**.
3. Refresh the OBS source (right-click → Refresh).

**After quitting NoClippy**

Run the bundled repair tool beside the installed app (or from Help while NoClippy is open):

```text
<install dir>\resources\noclippy-unstick.exe
```

Config and logs live under `%APPDATA%\app.noclippy.desktop\` (same path the app uses in dev if Tauri config dir is available).

**If it still does not show**

1. Fully quit the affected app (Steam: tray → Exit; Discord: quit from the app).
2. Reopen the app.
3. Refresh OBS.

**If you removed the app from NoClippy before cleaning up**, NoClippy may not know that
app anymore. It only clears flags on apps you've added (or that it recorded hiding), and
it never touches other apps that hide themselves from capture, like GPU overlays.
Quitting and reopening the app always clears it, or add the app again and run the fix
above.

**If Windows blocks the clear** (access denied in logs): run NoClippy or `noclippy-unstick.exe` **as administrator**, or restart the affected app.

---

## Protection toggle stopped re-hiding windows

1. Click **Fix stuck windows** on the main screen and follow what it suggests.
2. Use **Re-hide everything** (in the result, or **Help → Troubleshoot and recover**).
3. If duplicate NoClippy copies are running, close extras first (the main screen shows a banner).

---

## Invisible hiding fails for an app

When invisible hiding fails, NoClippy covers the app with a black bar on stream
automatically, and you see the bar too. If even that fails, it minimizes the window and
tells you which app.

| Symptom | What to try |
|--------|-------------|
| Injection error in the log | Antivirus may have quarantined NoClippy's `noclippy_injector` file. Restore it or reinstall. The app gets a black bar meanwhile. |
| Access denied | The app may be running as administrator. Run NoClippy as administrator too, or live with the black bar. |
| 32-bit app | It can't be hidden invisibly, so it always gets a black bar. |
| Anti-cheat game | NoClippy never injects into these, to avoid a ban. They always get a black bar. |
| Electron app black on your own screen | Turn off **Hardware acceleration** in that app and restart it. |
| Uncommon app | Any program works unless it's an anti-cheat game. Use the exact `.exe` name from Task Manager → Details. |

---

## Steam, Discord, Chrome, Slack (multi-process apps)

These apps use helper processes (e.g. `steamwebhelper.exe`, `msedgewebview2.exe`). NoClippy:

- Applies and clears WDA on **all PIDs** for the rule's process name and known related names.
- Scans **child windows**, not only the top-level window, when restoring flags.
- Records injected targets on disk so **quit**, **startup**, and **unstick** can restore even after a crash.

**Steam specifically:**

Steam runs as three separate processes: `steam.exe` (launcher), `steamwebhelper.exe` (the actual UI windows), and `gameoverlayui.exe` (in-game overlay). You can match any of them in your rule — NoClippy automatically sweeps all three on quit and restore. Before v0.1.0 (unreleased), only `steamwebhelper.exe` as the rule target swept the full family; direct rules on `steam.exe` left the helper windows stuck.

If Steam is still invisible after using NoClippy v0.1.0+ and the repair tool:
1. Fully exit Steam (system tray → Exit).
2. Reopen Steam.
3. Refresh the OBS source.

Rule tip: match the **main UI process** shown in Task Manager (Steam → `steam.exe` or `steamwebhelper.exe`, Discord → `Discord.exe`).

---

## NoClippy UI frozen or not opening

1. Check the system tray; left-click opens the dashboard.
2. Only one `noclippy-desktop.exe` should run (Help shows a warning if duplicates exist).
3. **Help → NoClippy window stopped responding → Restart NoClippy**.
4. Logs: **Help → Open logs** (`%APPDATA%\app.noclippy.desktop\logs\`).

---

## OBS / streaming

- Connection issues: **Settings → Streaming** (OBS WebSocket, auto-protect when live).
- **Black bar** leak warnings use pixel sampling; **Invisible on stream** does not (no pixel check).
- After fixing stuck flags, always **refresh** the OBS source.

---

## What NoClippy does automatically

| Event | Behavior |
|-------|----------|
| Protection turned **off** | Full capture-flag restore for rule targets + tracked apps |
| **Quit** NoClippy (tray → Quit) | Same restore before exit |
| **Startup** | Restore stuck flags from last run, then re-apply if protection was left on |
| **Crash / kill** | Next startup restore; or run `noclippy-unstick.exe` manually |

---

## Copy a report for support

**Settings → Help → Copy report**, or Discord / GitHub issue with logs from **Open logs**.

Include: protection on/off, whether the app was invisible or showed a black bar, the app's program name, and whether NoClippy was running when the app stayed invisible.
