# VeriCore Windows Release Guide

This document explains how VeriCore should become a normal Windows application that users download, install, and update.

## The Product Model

End users should not install Node.js, Python, npm packages, or pip packages.

For users, VeriCore should be:

1. A downloadable Windows installer, for example `VeriCore Setup 1.0.0.exe`.
2. An installed desktop app with Start Menu shortcuts.
3. A bundled Python engine that runs in the background when the app opens.
4. A signed executable so Windows SmartScreen trust improves over time.
5. An app that can check GitHub Releases, download newer versions, and apply updates.

## Recommended Stack

- Electron for the desktop shell.
- PyInstaller for packaging `engine/main.py` into `vericore-engine.exe`.
- Electron Builder for creating the Windows installer.
- NSIS as the Windows installer target.
- GitHub Releases as the initial public update channel.
- `electron-updater` for in-app updates after releases are stable.

## Build Flow

From `ui`:

```powershell
npm run build:engine
npm run build
```

`npm run build:engine` creates:

```text
engine/dist/vericore-engine.exe
```

`npm run build` packages the Electron app and copies that engine binary into:

```text
resources/engine/vericore-engine.exe
```

At runtime, `ui/main.js` starts that bundled engine automatically.

## Administrator Access

VeriCore needs Administrator access for deeper hardware checks. The packaged app now checks whether it is elevated. If not, it relaunches itself with the Windows `RunAs` verb, which triggers the UAC prompt.

In development, run PowerShell or Command Prompt as Administrator before starting the engine manually.

Important: requesting Administrator access is different from code signing. Admin access controls permissions. Code signing helps Windows and antivirus systems trust the installer.

## Code Signing

Before public distribution, buy a Windows code signing certificate.

Recommended path:

- Start with a standard OV code signing certificate if budget is tight.
- Use EV signing later if SmartScreen reputation becomes a serious barrier.
- Store signing credentials in GitHub Actions secrets.
- Configure Electron Builder signing only in CI, not directly in source control.

Do not publish unsigned installers as the main public download once real users are involved.

## Auto-Updates

The clean first version is:

1. Publish releases on GitHub.
2. Users manually download the latest installer.
3. Add in-app update checks once installer signing and release versioning are stable.

The next version should add:

- `electron-updater`
- a `publish` section in `ui/package.json`
- an app menu or Settings button for "Check for Updates"
- automatic check on startup, probably delayed by a few seconds

Electron Builder can publish update metadata to GitHub Releases. `electron-updater` reads that metadata, downloads the new installer, and installs it after the user confirms or when the app quits.

## Versioning

Use semantic versions:

```text
1.0.0
1.0.1
1.1.0
2.0.0
```

Recommended meaning:

- Patch: bug fixes and small hardware collector fixes.
- Minor: new diagnostics, report sections, or UI features.
- Major: changes to report format, licensing, cloud sync, or engine architecture.

The version in `ui/package.json` should be the release version. Keep visible app version text in the UI synchronized with it before release.

## GitHub Actions Release Flow

A practical CI workflow should:

1. Run on version tags like `v1.0.0`.
2. Install Python.
3. Install engine dependencies.
4. Build `vericore-engine.exe` with PyInstaller.
5. Install Node dependencies.
6. Build the Electron installer with Electron Builder.
7. Sign the installer.
8. Upload installer artifacts to GitHub Releases.

Example tag flow:

```powershell
git tag v1.0.0
git push origin v1.0.0
```

## Pre-Release Checklist

- Add final `src/assets/icon.ico` and `src/assets/icon.png`.
- Build and test on a clean Windows machine.
- Confirm the UAC prompt appears on packaged launch.
- Confirm the bundled engine starts without Python installed.
- Run a scan as Administrator.
- Run active diagnostics.
- Export a report.
- Verify installer uninstall works.
- Sign the installer.
- Publish a GitHub Release.

