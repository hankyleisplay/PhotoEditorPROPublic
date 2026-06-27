<div align="center">

# 📷 Photo Editor PRO

**A professional-grade, Electron-based photo editor powered by a Rust/WebAssembly image processing engine.**

![Platform](https://img.shields.io/badge/platform-Windows-blue?style=flat-square)
![Version](https://img.shields.io/badge/version-1.0.0-6366f1?style=flat-square)
![Electron](https://img.shields.io/badge/Electron-31-47848F?style=flat-square&logo=electron)
![Rust](https://img.shields.io/badge/Rust-WASM-orange?style=flat-square&logo=rust)
![License](https://img.shields.io/badge/license-Commercial-red?style=flat-square)

</div>

---

## Overview

**Photo Editor PRO** is a feature-rich, desktop photo editing application built with **Electron** and powered by a native **Rust + WebAssembly** core engine for hardware-accelerated pixel processing. It offers Lightroom-style tone controls, HSL mixing, spline curve editing, artistic frames, vector doodle drawing, and automatic GitHub release updates — all wrapped in a secure, obfuscated, closed-source package.

---

## ✨ Features

### 🎨 Image Editing
| Category | Features |
|---|---|
| **Basic Tone** | Exposure, Contrast, Temperature, Tint, Saturation |
| **Advanced Tone** | Highlights, Shadows, Whites, Blacks |
| **Presence** | Vibrance, Clarity, Dehaze |
| **Detail & Effects** | Sharpening, Noise Reduction, Vignette, Film Grain |
| **Split Toning** | Highlight Hue/Sat, Shadow Hue/Sat, Balance |
| **HSL Color Mixer** | Per-channel Hue, Saturation, Luminance (8 color ranges) |
| **Spline Curves** | RGB Master, Red, Green, Blue channels with Bézier splines |
| **Crop & Rotate** | Free/ratio crop, 90° rotation, flip H/V, fine straighten |
| **Watermark** | Custom text, size, opacity, color, position |
| **Artistic Frames** | Classic White, Polaroid Retro, Midnight Slate, Neon Glow |
| **Style Presets** | 11 built-in presets with blendable intensity slider |

### 🖌️ Drawing Tools
- **Brush & Eraser** with configurable size (1–100 px) and opacity (1–100%)
- **Color presets** (Red, Green, Blue, Yellow, White, Black) + **custom color picker**
- Strokes stored as **normalized vector coordinates** — scales perfectly on high-res exports
- Full **Undo / Redo** history integration

### 🔄 Auto-Update
- Checks [PhotoEditorPROPublic](https://github.com/hankyleisplay/PhotoEditorPROPublic) releases on startup
- Displays version comparison, changelog, and **real-time download progress bar**
- Automatically launches the new installer and quits the old instance

### 🛡️ Security & Licensing
- **Hardware-ID (HWID)** locking via motherboard UUID + CPU ID
- **Double SHA-256** salt-hashed license key generation
- JavaScript source protected by high-grade obfuscation (control flow flattening, dead code injection, Unicode escapes, string splitting, RC4/Base64 encoding)
- Rust WASM binary compiled with `opt-level = "z"`, `lto = true`, `strip = true` — all symbols removed

### ⚙️ Performance
- **Rust/WASM** core engine: all pixel-level processing (curves LUTs, HSL, sharpening, grain, vignette) runs in native-speed WebAssembly
- **Preview canvas** downscales input image for real-time filter preview; full-resolution rendering happens only on export
- Single-instance lock, background throttling, WebGL disabled — optimized memory footprint

---

## 🖥️ System Requirements

| Requirement | Minimum | Recommended |
|---|---|---|
| **OS** | Windows 10 (64-bit) | Windows 11 (64-bit) |
| **RAM** | 4 GB | 8 GB+ |
| **Display** | 1366 × 768 | 1920 × 1080 or higher |
| **Disk** | 300 MB free | 1 GB+ free |

---

## 🚀 Getting Started (Development)

### Prerequisites
- [Node.js](https://nodejs.org/) v18+
- [Rust toolchain](https://rustup.rs/) with `wasm32-unknown-unknown` target
- `wasm-pack` or manual `cargo build --target wasm32-unknown-unknown`

### Install Dependencies
```bash
npm install
```

### Compile Rust WASM Engine
```bash
cd src-rust
cargo build --release --target wasm32-unknown-unknown
cp target/wasm32-unknown-unknown/release/image_processor.wasm ../image_processor.wasm
```

### Run in Development
```bash
npm start
```

### Secure Production Build
```bash
node build-secure.js
```
This will:
1. **Backup** clean source files
2. **Obfuscate** `main.js`, `preload.js`, and `app.js` with maximum protection
3. **Package** via `electron-builder` into a portable `.exe` and NSIS installer
4. **Restore** clean source files for continued development

Output files are written to `dist/`:
- `Photo Editor PRO 1.1.0.exe` — Portable (no install required)
- `Photo Editor PRO Setup 1.1.0.exe` — NSIS installer

---

## 📁 Project Structure

```
Photo Editor PRO/
├── app.js                  # Renderer process: all UI logic, filters, drawing, i18n
├── index.html              # Main application markup
├── style.css               # All styling (dark theme, animations, layout)
├── main.js                 # Electron main process: window, IPC, license, updater
├── preload.js              # Context bridge: exposes licenseAPI, wasmAPI, updateAPI
├── build-secure.js         # Secure build pipeline (obfuscation + electron-builder)
├── package.json            # npm config and electron-builder config
├── image_processor.wasm    # Compiled Rust WebAssembly binary
├── src-rust/               # Rust source code
│   ├── Cargo.toml
│   └── src/
│       └── lib.rs          # Rust pixel processing functions
└── dist/                   # Packaged production output (git-ignored)
```

---

## 🔑 Licensing System

Photo Editor PRO uses an **offline hardware-locked licensing** scheme:

1. The app collects the system's **Motherboard UUID** and **CPU ID** via PowerShell
2. Both values are combined and processed through a **double SHA-256 salted hash** to produce a unique `XXXXX-XXXXX-XXXXX-XXXXX` license key
3. The user obtains their **HWID** from the activation overlay and provides it to the developer
4. The developer generates the matching key and supplies it to the user
5. The key is stored locally in `%AppData%/photo-editor-pro/license.json`

> ⚠️ License keys are non-transferable and tied to a single machine.

---

## 🔒 Security Notes

This application is **closed-source** and protected against reverse engineering:

- JavaScript sources are obfuscated using `javascript-obfuscator` with maximum settings
- The WASM binary is compiled with all debug symbols stripped
- Right-click context menu and DevTools shortcuts (F12, Ctrl+Shift+I, Ctrl+U) are disabled
- The application enforces a single-instance lock

> Any attempt to reverse engineer, decompile, or crack this software is strictly prohibited under copyright law.

---

## 📦 Auto-Update Architecture

```
[App Startup]
      │
      ▼ (after 3s delay)
[GitHub API] ──► GET /repos/hankyleisplay/PhotoEditorPROPublic/releases/latest
      │
      ▼ (if newer version found)
[Update Modal] ──► shows version, changelog, "Update Now" button
      │
      ▼ (on click)
[HTTPS Download] ──► streams .exe to %TEMP% with progress events
      │
      ▼ (on complete)
[exec installer] + [app.quit()]
```

Update checks are handled entirely in the **Electron main process** (`main.js`) and communicated to the renderer via secure IPC channels (`preload.js`).

---

## 📸 Supported Image Formats

| Format | Import | Export |
|---|---|---|
| JPEG / JPG | ✅ | ✅ |
| PNG | ✅ | ✅ |
| WebP | ✅ | ✅ |
| BMP | ✅ | ❌ |
| GIF (first frame) | ✅ | ❌ |

---

## 🙏 Third-Party Credits

| Component | License |
|---|---|
| [Electron](https://electronjs.org/) | MIT |
| [Rust toolchain](https://rust-lang.org/) | MIT / Apache 2.0 |
| [Lucide Icons](https://lucide.dev/) | ISC |
| [Plus Jakarta Sans](https://fonts.google.com/specimen/Plus+Jakarta+Sans) | OFL |
| [Noto Sans TC](https://fonts.google.com/noto/specimen/Noto+Sans+TC) | OFL |
| [javascript-obfuscator](https://github.com/javascript-obfuscator/javascript-obfuscator) | BSD-2-Clause |

---

## 📄 License

**Photo Editor PRO** is proprietary, commercially licensed software. All rights reserved.

Unauthorized copying, distribution, reverse engineering, or modification of this software is strictly prohibited. Violators will be subject to civil and criminal prosecution under applicable copyright laws.

---

<div align="center">

Made with ❤️ using Rust, WebAssembly, and Electron

</div>
