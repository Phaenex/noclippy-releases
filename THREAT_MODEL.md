# What NoClippy protects against (and what it can't)

NoClippy hides whole windows from your capture and from Alt+Tab. That covers a
real chunk of how people get burned on stream, but not all of it. Here's the
honest breakdown so you know where the edges are.

## What it covers

- **A private app window showing up in your capture.** Discord DMs, Slack, a
  password manager, your email, a messenger. You make a rule that matches the
  window by process name or title, and NoClippy covers it (black bar) or drops it
  from the capture entirely (Exclude mode). This is the main job.
- **An app you forgot about popping open mid-stream.** The window watcher checks
  for new windows about four times a second, so when a matched app appears it
  gets covered within a fraction of a second instead of sitting there exposed.
- **A window you never made a rule for.** Turn on **Lockdown** (deny-by-default)
  and NoClippy covers every window except the ones you mark stream-safe. That
  closes the gap for anything unexpected: it's covered on sight (within that same
  fraction of a second), not when you react, so there's no exposed window for a
  viewer to pause the stream on. The trade is that you have to mark your actual
  stream content (game, capture, scene) stream-safe, or it's covered too.
- **Alt+Tab exposure.** Per-rule "hide from Alt+Tab" pulls the window out of the
  Alt+Tab switcher (and its live thumbnail), so flipping through windows on stream
  doesn't flash Discord.
- **Booting unprotected.** "Turn protection on automatically when NoClippy starts"
  (on by default) means you never sit live with protection silently off.
- **Going live.** Optional OBS hook flips protection on when you start streaming.
- **A fast cover-everything.** The PANIC button and its hotkey slam every rule on
  at once when something's about to slip.

## What it can't cover

Be clear-eyed about these. NoClippy works at the window level, so anything that
isn't "a separate window it can match" is out of reach.

- **Private info shown *inside* an app you're intentionally capturing.** If your
  game's network menu shows your IP, or a website prints your IP on an error page,
  or your address is on a document you have open on purpose, that's content inside
  the window you're showing. NoClippy can't reach into a window and redact part of
  it. This is how a lot of the famous self-doxs happened.
- **System notification toasts and OS popups.** Windows notification banners, UAC
  prompts, and similar transient system surfaces are awkward to match reliably and
  can render as black bars in capture even for the OS itself. Handle these at the
  source: turn on Windows "Do not disturb" / Focus while live, and turn on Discord
  Streamer Mode (it suppresses Discord's own popups and hides invite codes).
- **Browser tabs and address-bar autocomplete.** A browser is one process with
  many tabs, so a process-name rule would hide the whole browser. Match by window
  title for a specific private tab, and lean on the browser's own privacy/guest
  mode for autocomplete.
- **Your webcam.** Reflections, a second monitor in frame, paperwork on the desk.
  Nothing software-side fixes a camera.
- **OBS overlays and widgets.** Chat boxes, alert boxes, and browser sources can
  print names, whispers, or donation messages. Those are your OBS scene, not a
  window NoClippy sees.

## Exclude mode vs the overlay modes

- **Black bar / covers (overlay modes):** work with zero setup, no injection.
  Safe by default. The downside is you see the cover too.
- **Exclude from capture:** the window stays normal on your screen but turns into
  a black box in the capture. This needs the Advanced injection toggle, because the
  only OS call that does it (`SetWindowDisplayAffinity`) has to run from inside the
  target process. If injection is off, an Exclude rule does nothing, so the app
  flags those rules with a clear "needs injection" warning instead of pretending
  they work.
- **The hardware-acceleration catch (Exclude mode).** On GPU-accelerated apps
  (Discord, Chrome, anything Electron), `SetWindowDisplayAffinity` exclude can turn
  the window black on your **own** monitor too, not just in the capture. It's a
  Windows/GPU compositing quirk, not something NoClippy can fix from outside the
  app. The fix is in that app: turn off Hardware Acceleration and restart it, and
  Exclude behaves as intended (gone from capture, normal for you). Or use a Black
  bar overlay, which never has this problem.

## Stuck capture flags (crash / force-quit)

If NoClippy crashes or is killed before cleanup, Windows can keep `WDA_EXCLUDEFROMCAPTURE`
(or a NoClippy blackout flag) on an app. The window stays invisible in OBS even after
NoClippy quits.

**Mitigations shipped in NoClippy:**

- Quit and startup paths run a full restore pipeline before remasking.
- `noclippy-unstick.exe` clears flags when the app is not running.
- In-app **Fix now** / **Full capture-flag repair** clears orphans while protection is on
  (without stripping active rules) and remasks afterward.
- Anti-cheat denylist processes are never injected or cleared (user must restart those apps).

## Not DRM

A phone pointed at your monitor beats any of this. NoClippy cuts down accidents.
It does not stop a determined leak.
