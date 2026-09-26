# Security Policy

NoClippy is a privacy tool, so security reports matter more than most. Thanks for taking the time.

## Reporting a vulnerability

Please **do not** open a public issue for a security problem.

Use GitHub's private reporting: go to the repo's **Security** tab → **Report a vulnerability**. That opens a private advisory only the maintainers can see.

Include what you'd expect: what the issue is, how to reproduce it, the impact, and your environment (Windows version, NoClippy version). A proof of concept helps a lot.

You'll get an acknowledgement within a few days. Once there's a fix, it ships in the next release and you'll be credited unless you'd rather not be.

## What's in scope

- The injector (`noclippy_injector*.dll`) and how NoClippy decides which processes it may load into. This is the most sensitive part: it loads a DLL into another process.
- The updater: signature verification, the manifest, the download path.
- The OBS WebSocket client and how the OBS password is stored (it lives in the OS credential store, never in `settings.json`).
- Any way to make NoClippy run code, leak the windows it's protecting, or exfiltrate data off the machine.

## What's not a vulnerability

- A phone camera pointed at the monitor beats any software masking. NoClippy reduces accidents, it is not DRM, and that limit is documented in [THREAT_MODEL.md](THREAT_MODEL.md).
- Antivirus flagging the injector. Process injection is inherently flag-prone. NoClippy only injects into apps you made a rule for, never into anti-cheat games or in-game overlays, and every file it ships is signed.
- A SmartScreen prompt on a new release. Builds are signed, but SmartScreen also wants download history. Expected, covered in the README.

## Supported versions

NoClippy 1.0 is the current supported release. Security fixes go
into the latest release. There are no back-ported patch branches for older versions.
