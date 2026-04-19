---
title: "Version 0.19.6.8"
date: 2026-04-19
version: "0.19.6.8"
weight: 1
---

[Download from GitHub](https://github.com/abkvme/taler/releases/tag/v0.19.6.8)

This release introduces the Staking UI with a duration selector, switches macOS distribution to fully static linking, upgrades core dependencies (Boost, OpenSSL, Qt) with ARM64 support, fixes icon theming on macOS and Windows, and ships extensive build system improvements.

<!--more-->

### Major Changes

#### Staking UI
- **"Start staking" panel** added to the Overview page with duration selector (1h / 6h / 24h / 7d / 30d)
- Live countdown timer showing remaining staking time with progress bar
- "Stop staking" button with confirmation dialog
- Passphrase prompt via existing wallet unlock dialog (new UnlockStaking mode)
- Auto-relock via QTimer when staking duration expires
- Staking panel hidden for unencrypted wallets (staking is always-on without encryption)
- Panel appears automatically after encrypting the wallet (no restart needed)
- Non-persistent: staking state resets on app restart (wallet starts locked)
- Full translations for all 34 supported languages

#### Icon Theme Fix
- All icons now render in **theme-adaptive color** on both light and dark themes
- Enabled icon colorization on **macOS and Windows** (previously only Linux)
- Icons use WindowText palette color, matching the rest of the UI text

#### Static Linking for macOS Distribution
- Switched macOS release builds from Homebrew dynamic libraries to a **fully static depends/ system**
- Release binaries no longer require Homebrew packages on user machines
- Eliminates "dyld: Library not loaded" crashes for boost@1.85 and other libraries

#### Dependency Upgrades (depends/ system)
- **Boost**: 1.64.0 → 1.88.0 (ARM64 macOS support, C++17)
- **OpenSSL**: 1.0.1k → 3.4.1 (ARM64 macOS support, security fixes, modern TLS)
- **Qt**: 5.9.6 → 5.15.16 (ARM64 macOS support, last Qt5 LTS)
- libevent: 2.1.8 → 2.1.12
- ZeroMQ: 4.3.1 → 4.3.5
- protobuf: 2.6.1 → 3.21.12
- qrencode: 3.4.4 → 4.1.1
- zlib: 1.2.11 → 1.3.1
- miniupnpc: 2.0.20180203 → 2.2.8
- **macOS minimum version**: 10.10 → 11.0 (required for Apple Silicon)

### Build System Enhancements
- macOS CI workflow rewritten to use the depends/ static build (matches Windows CI)
- `build_macos.sh` rewritten to use the identical depends/ flow as CI
- Only build tools (automake, libtool, pkg-config) needed from Homebrew
- `otool -L` verification step in CI to catch dynamic linking regressions
- Fixed dead Boost download URL (dl.bintray.com → archives.boost.io)
- Replaced deprecated `SSL_library_init()` with `OPENSSL_init_ssl()` for OpenSSL 3.x
- Added `-isysroot` to build_darwin_CC/CXX for macOS 15/26 SDKs
- Updated Qt 5.15 patches for the new source layout; disabled OpenGL and Vulkan for macOS (AGL removed in macOS 26 SDK)
- Updated miniupnpc build/stage paths for 2.2.8 layout and `UPNP_GetValidIGD` call for miniupnpc API 18
- Linux x64/ARM64 CI runners bumped from ubuntu-22.04 to ubuntu-24.04
- Linux CI and `build_linux.sh` now statically link Boost so binaries run on any Ubuntu regardless of installed libboost version
- All build workflows now trigger on pull_request against main for compile-only verification
- Bumped `actions/checkout` from v4 to v5 across all workflows
- `build_linux.sh` pre-checks system libs via pkg-config and points to `--install-deps` if missing
- Added `build_windows.sh` for cross-compile from Ubuntu via MinGW-w64, matching the Windows CI step-for-step
- Windows cross-compile: `WINDRES` set per host for OpenSSL 3.4.1 MinGW; Qt patched with `-no-feature-schannel` and OpenSSL 3.x link-test for MinGW
- Qt: replaced `-dbus-runtime` with `-no-dbus` globally (D-Bus unused by taler-qt)
- Added retry-once to the depends/ build step to work around an intermittent Qt 5.15 moc/plugin parallel-build race
- macOS: link CoreVideo, IOSurface, Carbon, QuartzCore and Metal frameworks so Qt's static `libqcocoa.a` resolves on macOS 26 SDK
- Windows: link wtsapi32, userenv, netapi32 and `Qt5WindowsUIAutomationSupport` static library so `libqwindows.a` links correctly
- Fixed plugin link-test ordering so platform library additions run before the QMinimalIntegrationPlugin test
- Dropped obsolete `__MINGW32__` Boost 1.85+ `copy_option` branch in wallet db code
- `build_macos.sh` gained `clean` and `clean-all` subcommands and auto-runs `autogen.sh` when configure.ac / M4 / Makefile.am files are newer than configure (`build_windows.sh` gains the same behavior)
- Rewrote `README.md` as a modern open-source project landing page with CI/metadata badges, SEO description, Docker quick-start, and cross-platform self-compile pointers

### Docker Images
Pre-built multi-architecture Docker images are available on GitHub Container Registry:

```bash
# Pull this version
docker pull ghcr.io/abkvme/taler:0.19.6.8

# Pull latest
docker pull ghcr.io/abkvme/taler:latest

# Quick start with Docker Compose
curl -OL https://github.com/abkvme/taler/releases/download/v0.19.6.8/docker-compose.yml
docker-compose up -d
```

Supported architectures: `linux/amd64`, `linux/arm64`

### Technical Details
- **Version**: 0.19.6.8
- **Boost**: 1.88.0
- **OpenSSL**: 3.4.1
- **Qt**: 5.15.16
- **Supported Platforms**:
  - Linux x64, Linux ARM64
  - Windows x64
  - macOS (Apple Silicon, macOS 11.0+)
- **Docker Images**: Available for linux/amd64 and linux/arm64

### Breaking Changes
- **macOS minimum version raised to 11.0** (was 10.10) — required for Apple Silicon and modern toolchain
- macOS release binaries are now fully statically linked; Homebrew is no longer a runtime requirement

### Upgrade Notes
- macOS users on 10.10–10.15 must stay on 0.18.44.7 or upgrade their OS
- Docker users should pull the latest multi-arch images from `ghcr.io/abkvme/taler`
- No wallet format changes in this release; wallet.dat files are compatible with 0.18.44.7
