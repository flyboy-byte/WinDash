# WinDash

A small toggle dashboard for Windows 11 tweaks — power, privacy, background services and look & feel — with an always-on desktop widget built on [Rainmeter](https://www.rainmeter.net/).

Made for a [ReviOS](https://revi.cc/) install, but the switches are plain Windows settings (registry policies, services, scheduled tasks, power plans) and work on any Windows 11 PC.

## What it does

| Section | Switches |
|---|---|
| **Power** | Auto power plan (Ultra Performance plugged in, Balanced on battery — switches on plug/unplug via a scheduled task), Ultra Performance plan |
| **Privacy** | Location services, Delivery Optimization peer-to-peer, Copilot, suggested/promoted apps, leftover data-collection tasks, Maps / sync / link-tracking services |
| **Background & battery** | Search indexing, SysMain, print spooler, Edge/WebView2 updater, Store apps in background |
| **Look & feel** (full window only) | Classic right-click menu, file extensions, hidden files, centered taskbar, dark mode, transparency |

The desktop widget also shows live CPU, GPU, RAM, disk and network usage, with links to Task Manager and [Chris Titus Tech's WinUtil](https://github.com/ChrisTitusTech/winutil).

Every switch reads the real current setting, so it never drifts from what Windows is actually doing. Every change is logged to `windash.log` next to the script.

## Requirements

- Windows 11, Windows PowerShell 5.1 (built in)
- Administrator rights (the script relaunches itself elevated)
- [Rainmeter](https://www.rainmeter.net/) 4.5+ for the desktop widget (`winget install Rainmeter.Rainmeter`)

## Usage

```powershell
# Full window
powershell -NoProfile -ExecutionPolicy Bypass -STA -File WinDash.ps1

# Generate the Rainmeter widget, then load it
powershell -NoProfile -ExecutionPolicy Bypass -File WinDash.ps1 -BuildSkin
& "$env:ProgramFiles\Rainmeter\Rainmeter.exe" !ActivateConfig WinDash WinDash.ini
```

To sync the widget and start Rainmeter at login, put a shortcut to this in your Startup folder:

```
powershell.exe -NoProfile -ExecutionPolicy Bypass -WindowStyle Hidden -File "C:\path\to\WinDash.ps1" -Launch
```

### Command line

| Flag | Does |
|---|---|
| `-Status` | Print every switch and its current state |
| `-Flip <id>` | Flip one switch (ids are shown by `-Status`) |
| `-Export` | Re-read all states into the widget |
| `-BuildSkin` | (Re)generate the Rainmeter widget from the toggle list |
| `-RestartExplorer` | Restart Explorer so Look & feel changes show |
| `-WinUtil` | Open Chris Titus Tech's WinUtil in an admin window |
| `-Launch` | Sync the widget, then start Rainmeter |

## Adding a switch

Add an entry to `$Toggles` in `WinDash.ps1` with `Get` / `On` / `Off` script blocks, give it a short id in `$ToggleIds`, then run `-BuildSkin`. The widget is generated from the same list, so the two always match.

## Notes

- Switches change system settings. Most are policies or service start types that can be flipped back at any time, but read the descriptions (hover in the widget) before turning things off.
- Rainmeter widget actions run PowerShell through the bundled RunCommand plugin with a hidden window. A click takes well under a second; the switch animates immediately and confirms once the change is applied.
