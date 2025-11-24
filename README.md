# TALER Website

Community-driven website for TALER cryptocurrency, built with Hugo static site generator.

**Live Site**: [taler.tech](https://taler.tech)

---

## Contributing

We welcome contributions to improve the TALER website! Whether you want to:

- Fix typos or improve documentation
- Improve translations
- Add new content or pages
- Report bugs or issues
- Add missing documentation

### How to Contribute

1. **Fork the repository**
   ```bash
   git clone https://github.com/abkvme/taler.tech.git
   cd taler.tech
   ```

2. **Create a new branch**
   ```bash
   git checkout -b feature/your-improvement
   ```

3. **Make your changes** (see Content Management below)
   - Edit documentation in `/content/docs/`
   - Update translations in `/i18n/`
   - Test locally with `hugo server -D`

4. **Commit your changes**
   ```bash
   git add .
   git commit -m "Description of your changes"
   ```

5. **Push to your fork**
   ```bash
   git push origin feature/your-improvement
   ```

6. **Submit a Pull Request**
   - Go to the original repository on GitHub
   - Click "New Pull Request"
   - Select your fork and branch
   - Describe your changes clearly
   - Submit for review

### Contribution Guidelines

- **Documentation**: Keep it clear, concise, and accurate
- **Translations**: Update all 4 languages consistently (en, ru, be, uk)
- **Testing**: Test changes locally before submitting
- **Commit messages**: Use clear, descriptive commit messages

### Questions?

- Open an issue on GitHub
- Join our [Telegram community](https://t.me/talercommunity)

---

## Content Management

### Website Structure

The website contains the following sections:

- **Home** - Overview of TALER blockchain with key features
- **Downloads** - Download links for wallets (Windows, macOS, Linux, ARM64)
- **Links** - Community resources, explorers, exchanges
- **FAQ** - Frequently asked questions
- **Documentation** - User guides, node operator guides, developer API reference
- **Release Notes** - Version history and changelogs

### Documentation Organization

Documentation is organized into three main sections:

**User Documentation:**
- Getting Started
- Wallet Setup
- Sending & Receiving
- Security Best Practices
- Mining & Staking

**Node Operator Documentation:**
- Running a Node
- Command Line (talerd)
- Docker Deployment

**Developer Documentation:**
- RPC Reference (145+ API methods)

---

## Managing Documentation

### Editing Existing Documentation

Documentation files are located in `/content/docs/` with separate files for each language:

```
content/docs/
├── getting-started/
│   ├── index.en.md    # English
│   ├── index.ru.md    # Russian (Русский)
│   ├── index.be.md    # Belarusian (Беларуская)
│   └── index.uk.md    # Ukrainian (Українська)
├── wallet-setup/
├── security/
└── ...
```

**To update documentation:**

1. Edit the appropriate `.md` file in `/content/docs/[section]/`
2. **Important**: Update all 4 language versions (en, ru, be, uk)
3. Keep markdown formatting intact

**Example frontmatter:**
```yaml
---
title: "Page Title"
weight: 1
---
```

### Adding Release Notes

Create a new directory in `/content/release-notes/` with the version number:

```
content/release-notes/
└── v0.18.44.7/
    ├── index.en.md
    ├── index.ru.md
    ├── index.be.md
    └── index.uk.md
```

**Frontmatter for releases:**
```yaml
---
title: "Version 0.18.44.7"
date: 2025-11-23
version: "0.18.44.7"
weight: 1
---

[Download from GitHub](https://github.com/abkvme/taler/releases/tag/v0.18.44.7)

Short release summary.

<!--more-->

## Detailed changes
Full changelog here...
```

**Note**: The `<!--more-->` separator controls what appears on the release notes list page (everything before it) vs. the full release page.

### Updating Translations

Translation strings are stored in `/i18n/` directory:

```
i18n/
├── en.toml    # English
├── ru.toml    # Russian
├── be.toml    # Belarusian
└── uk.toml    # Ukrainian
```

**Format:**
```toml
[key_name]
other = "Translated text"
```

### Updating Download Links

When a new release is published, update the download links in `/layouts/downloads/list.html`:

```html
<a href="https://github.com/abkvme/taler/releases/download/v0.18.44.7/taler-v0.18.44.7-win64.zip"
   class="btn btn-taler">
```

Update all platform download links (Windows x64, macOS ARM64, Linux x64, Linux ARM64).

---

## Local Development

### Prerequisites

- [Hugo Extended](https://gohugo.io/installation/) v0.112.0 or later

### Running Locally

```bash
# Start development server
hugo server -D

# Open browser at:
http://localhost:1313/en/
```

### Build for Production

```bash
# Build optimized static site
hugo --gc --minify

# Output will be in ./public/ directory
```

---

## Languages

The website supports 4 languages with automatic browser detection:

1. **English** (en) - Default
2. **Беларуская** (be) - Belarusian
3. **Русский** (ru) - Russian
4. **Українська** (uk) - Ukrainian

Users can manually switch languages using the language selector in the navigation bar.

---

## Deployment

The site is a static website that can be deployed to:

- **GitHub Pages** - Push `./public/` to gh-pages branch
- **Netlify / Vercel** - Connect repository, set build command: `hugo --gc --minify`
- **AWS S3** - Upload `./public/` directory
- Any static hosting service

---

## Links

- **Website**: [taler.tech](https://taler.tech)
- **GitHub**: [github.com/abkvme/taler](https://github.com/abkvme/taler)
- **Telegram**: [@talercommunity](https://t.me/talercommunity)
- **Explorers**:
  - [explorer.talercoin.org](https://explorer.talercoin.org/)
  - [explorer.talercrypto.com](https://explorer.talercrypto.com/)

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

Copyleft - All wrongs reserved.
