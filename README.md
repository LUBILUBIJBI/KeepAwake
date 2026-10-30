# KeepAwake

**A single-button Windows app that keeps your screen on and stops the PC from going to sleep.**

No installer, no background service, no settings changed. One 10 KB `.exe` — click once to stay
awake, click again and Windows behaves exactly as before.

![KeepAwake in OFF and ON state](screenshot.png)

---

## Why

Watching a video, reading a long page, monitoring a dashboard, running a long download — and
Windows dims the screen or drops into sleep. KeepAwake is the smallest possible fix: one window,
one button, nothing else.

## Features

- **One button, two states** — grey **OFF** (normal Windows behaviour) / green **ON** (stay awake).
- **Keeps the display on** *and* **prevents system sleep/hibernate**.
- **Nothing persists** — closing the window releases the lock immediately; your power settings are
  never modified.
- **No dependencies** — plain WinForms, runs on any Windows 7/8/10/11 with the .NET Framework
  (preinstalled everywhere). No installer, no admin rights, no network access.
- **10 KB** single executable.
- Self-correcting: re-asserts the lock every 30 seconds in case another app resets it.

## Usage

1. Download `KeepAwake.exe` and double-click it.
2. Click the big button → it turns green: screen and PC stay on.
3. Click it again → back to normal. Or just close the window.

| State | Meaning |
|-------|---------|
| **OFF** (grey) | Normal mode. Screen and PC turn off according to your Windows power settings. |
| **ON** (green) | Screen stays on, the PC will not enter sleep or hibernate. |

On first start Windows SmartScreen may warn because the executable is not code-signed:
choose **More info → Run anyway**.

## How it works

While ON, the app calls the Windows API:

```c
SetThreadExecutionState(ES_CONTINUOUS | ES_DISPLAY_REQUIRED | ES_SYSTEM_REQUIRED);
```

`ES_DISPLAY_REQUIRED` keeps the display on, `ES_SYSTEM_REQUIRED` keeps the system out of
sleep/hibernate, and `ES_CONTINUOUS` makes the request last until it is reset. On OFF — and on
window close — it calls:

```c
SetThreadExecutionState(ES_CONTINUOUS);
```

which hands control back to the normal power plan. That is the whole program (~150 lines of C#).

## Limitations

- Only active while the window is open (this is intentional — nothing is left behind).
- Does not prevent a manual shutdown/restart or a forced Windows Update restart.
- Does not prevent sleep caused by closing a laptop lid (that is a power-plan setting).
- Does not change power plans, screensaver or lock-screen policy.

## Build from source

Requires only the .NET Framework compiler that ships with Windows — no SDK, no Visual Studio.

```powershell
powershell -ExecutionPolicy Bypass -File .\build.ps1
```

The script generates `app.ico` and compiles `KeepAwake.cs` into `KeepAwake.exe`.

| File | Purpose |
|------|---------|
| `KeepAwake.cs` | Full source (WinForms UI + `SetThreadExecutionState`) |
| `build.ps1` | One-command build with `csc.exe` |
| `app.ico` | Generated icon |
| `KeepAwake.exe` | Ready-to-run application |

## License

MIT — free to use, modify and redistribute.
