---
title: "Version 0.18.44.7"
date: 2025-11-23
version: "0.18.44.7"
weight: 1
---

[Download from GitHub](https://github.com/abkvme/taler/releases/tag/v0.18.44.7)

This release brings major infrastructure upgrades and platform support improvements, including a complete database engine upgrade, ARM architecture support, and automated deployment capabilities.

<!--more-->

### Major Changes

#### Berkeley DB Upgrade (v4 → v18.1.40)
- **Complete database engine upgrade** from Berkeley DB 4.8 to 18.1.40
- Standardized BDB 18.1.40 across all platforms (Linux, Windows, macOS, ARM64)
- Added new installation script `contrib/install_db18.sh` for automated BDB 18 setup
- Fixed MinGW compilation issues with custom atomics patch for Windows builds
- Improved database performance and reliability
- Fixed documentation installation issues in BDB build process

#### ARM Architecture Support
- **Native ARM64 (aarch64) support** added for Linux and macOS platforms
- **Apple Silicon (M1/M2/M3+) native support** for macOS
- New GitHub Actions workflow for automated ARM64 builds
- Full compatibility with ARM-based servers and devices
- Optimized performance on Apple Silicon Macs and ARM server deployments
- Native builds eliminate Rosetta 2 translation overhead on M1+ Macs

#### Updated Application Icon
- **New redesigned TALER icon** now used in the application
- Modern icon design adopted by the TALER community
- Consistent branding across all platforms
- Updated application icons for Windows, macOS, and Linux

#### Network Infrastructure Updates
- **New DNS seeds added**: taler.tech domain seeds configured
- Added fixed IP seed for improved network reliability
- Enhanced peer discovery and network connectivity

#### Automated Deployment & CI/CD
- **Docker multi-architecture support** (linux/amd64, linux/arm64)
- Automated Docker image publishing to Docker Hub
- 18x speedup in Docker builds using native architecture runners
- GitHub Actions workflows for all platforms:
  - Linux x64 and ARM64
  - Windows x64
  - macOS (Apple Silicon)
- Concurrency controls to optimize CI/CD resources
- Automated release artifact generation

### Build System Enhancements
- Qt 5.9.6 patch for GCC 11+ compatibility
- Fixed Qt moc compilation for cross-platform builds
- Improved dependency management (protobuf, libzmq5, libqrencode4)
- Better handling of MinGW cross-compilation
- Fixed executable permissions for build scripts

### Technical Details
- **Version**: 0.18.44.7
- **Berkeley DB**: 18.1.40 (upgraded from 4.8)
- **Supported Platforms**:
  - Linux x64, Linux ARM64
  - Windows x64
  - macOS (Apple Silicon)
- **Docker Images**: Available for linux/amd64 and linux/arm64
- **Qt Version**: 5.9.6 with GCC 11+ patches

### Breaking Changes
- **Berkeley DB upgraded from 4.8 to 18.1.40** - wallet.dat files are NOT backward compatible
- Once upgraded to this version, wallet.dat files cannot be used with older TALER versions
- This is a one-way upgrade

### Upgrade Notes
- **Warning**: After upgrading, you cannot downgrade to older TALER versions
- Wallet.dat files will be automatically upgraded to BDB 18.1.40 format on first use
- The BDB upgrade is required for ARM64 platform support (BDB 4.8 lacks ARM64 mutex support)
- Docker users should pull the latest multi-arch images
- See README-DOCKER.md for updated deployment instructions
