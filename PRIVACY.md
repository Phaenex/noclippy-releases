# Privacy: what NoClippy can see, and where it stays

NoClippy is a tool for keeping your private windows off your stream, so it would
be backwards if the tool itself were sketchy about your data. Here is exactly what
it does on your PC, in plain terms. Short version: it watches your open windows so
it knows what to cover, and none of that ever leaves your machine.

## What it can see

To do its job, NoClippy reads the list of your **open windows**: each window's
title, the app (process) name, its window class, and its position and size on
screen. It uses that to match your rules ("cover Discord") and to place a cover in
the right spot, and to keep the cover glued to the window as it moves.

That window list is read live and used in the moment. It is not logged, not saved,
and not sent anywhere.

## What it never does

- It never reads the **contents** of a window: your messages, your email, the page
  you're on. It only paints a cover on top of windows you choose.
- It never reads your keystrokes, clipboard, microphone, or camera.
- It has no account, no sign-in, no telemetry, and no analytics. It does not phone
  home about how you use it.

## What it stores, and where

Everything is stored locally on your PC, in your user profile:

- Your **profiles and rules** (the app names and title keywords you choose to
  cover) and your settings, in a `settings.json` / profile file under your local
  app data.
- Your **OBS WebSocket password**, if you set one, goes into the Windows
  Credential Manager (the OS keyring), never into a plain file.

Uninstalling removes the app; you can delete the config folder to remove the rest.

## The only network it makes

- **Update check.** On launch it asks GitHub whether a newer signed version exists,
  so it can offer a one-click update. That is a normal request to GitHub, with no
  personal data attached.
- **OBS, on your own PC.** If you connect the optional OBS integration, it talks to
  OBS over WebSocket on your own machine (localhost). Nothing leaves your computer.

There are no other servers. NoClippy has no backend.

## Advanced hiding (injection), opt-in

The "Invisible on stream" / "Black box" modes make a window vanish from the capture
itself. Windows only lets a program do that to a window it owns, so to apply it to
another app (like Discord) NoClippy injects a small helper (`noclippy_injector.dll`)
into that app. This is the only time NoClippy touches another program, it is off by
default, and antivirus may ask about it once because injection is a flagged
technique. The default black-bar cover needs none of this.

## You can verify all of this

NoClippy is free but closed source, so here's how to check it without taking my word:

- **It's signed.** Every NoClippy exe, DLL and installer carries a Microsoft-verified
  signature for Nicholas DAmato (right-click → Properties → Digital Signatures). If
  that tab is missing or names someone else, it isn't a real NoClippy build.
- **Watch its connections.** Resource Monitor → Network, or any firewall. You'll see a
  GitHub check for updates when it starts, a connection to your own OBS or Streamlabs
  on this PC if you set one up, and nothing else. There's no telemetry and no account.
- **Your data is a file you can open.** Rules and settings are plain JSON in
  `%APPDATA%pp.noclippy.desktop`. Delete that folder and it's all gone.

If you ever see NoClippy do something this page doesn't describe, that's a bug. Please
open an issue at https://github.com/Phaenex/noclippy-releases/issues.
