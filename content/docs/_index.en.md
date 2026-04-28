---
title: "Documentation"
description: "Complete documentation for TALER Blockchain - guides for users, developers, and validators"
---

Welcome to the TALER Blockchain documentation. Here you'll find comprehensive guides and references for all aspects of the platform.

## Source Code & Repositories

The TALER ecosystem is open source. All projects live on GitHub:

| Project | Repository | Purpose |
|---|---|---|
| **Taler Core** | [github.com/abkvme/taler](https://github.com/abkvme/taler) | Node daemon (`talerd`), CLI (`taler-cli`), and GUI wallet (`taler-qt`). Latest releases for Linux, macOS and Windows. |
| **Taler Explorer** | [github.com/abkvme/taler-explorer](https://github.com/abkvme/taler-explorer) | Block explorer that powers [explorer.taler.tech](https://explorer.taler.tech/). Single-binary Go service that talks to a `talerd` over JSON-RPC. |
| **taler.tech** | [github.com/abkvme/taler.tech](https://github.com/abkvme/taler.tech) | This documentation website (Hugo). |
| **Legacy Core** | [github.com/cryptadev/taler](https://github.com/cryptadev/taler) | Historical Taler source up to v0.17.2.6. Use the current Taler Core repo above for anything new. |

Pre-built container images are published on GitHub Container Registry:

```
docker pull ghcr.io/abkvme/taler:latest             # Taler node
docker pull ghcr.io/abkvme/taler-explorer:latest    # Block explorer
```

## Contribute to Documentation

This documentation is community-driven and open for contributions. You can help improve it by:

- Fixing typos or unclear explanations
- Adding missing information
- Improving translations
- Updating outdated content

**How to contribute:**

1. Visit the website repository: [github.com/abkvme/taler.tech](https://github.com/abkvme/taler.tech)
2. Fork the repository and make your changes
3. Submit a pull request with your improvements

All documentation files are in `/content/docs/` with versions for English, Russian, Belarusian, and Ukrainian. See the repository README for detailed contribution guidelines.
