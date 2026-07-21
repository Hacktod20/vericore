# VeriCore

VeriCore is a Windows hardware inspection and authenticity platform. It combines a desktop Electron interface with a Python inspection engine that reads system, firmware, registry, storage, battery, display, GPU, network, TPM, and diagnostic data, then cross-checks the results for inconsistencies.

The goal is simple: do not only trust what the operating system reports. Verify it.

## What It Does

- Scans hardware identity across multiple Windows data sources.
- Checks for mismatches that may indicate spoofing, firmware issues, or unreliable system reporting.
- Scores device health and authenticity.
- Runs active CPU, memory, storage, and thermal diagnostic workflows.
- Exports inspection reports as JSON.
- Falls back to demo data when the engine is unavailable during development.

## Project Structure

```text
vericore/
  engine/        Python inspection and diagnostics engine
  ui/            Electron desktop application
  docs/          Project and release documentation
```

The UI and engine communicate locally over WebSocket on `ws://127.0.0.1:7473`.

## Development Setup

These steps are for contributors. End users should install VeriCore from a signed Windows installer, not install Node.js or Python.

### 1. Install Engine Dependencies

```powershell
cd C:\vericore\engine
pip install -r requirements.txt
```

### 2. Install UI Dependencies

```powershell
cd C:\vericore\ui
npm install
```

### 3. Run Locally

Start the engine:

```powershell
cd C:\vericore\engine
python main.py
```

Start the UI:

```powershell
cd C:\vericore\ui
npm start
```

For full hardware access during development, run your terminal as Administrator before starting the engine.

## Packaging For Windows

VeriCore is intended to ship as a Windows installer. The user downloads an installer, installs the app, launches VeriCore, and the bundled engine runs in the background.

Build the Python engine:

```powershell
cd C:\vericore\ui
npm run build:engine
```

Build the Windows installer:

```powershell
npm run build
```

The installer output is created under `ui\dist`.

Packaged Windows builds request Administrator elevation on launch so the inspection engine can access deeper hardware information.

## Releases And Updates

Use GitHub Releases as the first release channel:

1. Bump `ui/package.json` version.
2. Build the engine with PyInstaller.
3. Build the Electron installer with Electron Builder.
4. Upload the generated installer artifacts to a GitHub Release.
5. Later, wire `electron-updater` so the app checks GitHub Releases and downloads updates automatically.

See [docs/release.md](docs/release.md) for the complete release plan.

## Current Status

VeriCore is usable as a development build and has the main product foundation in place. Before public release, the project still needs production signing certificates, final installer icons/assets, auto-update wiring, and a release workflow.

