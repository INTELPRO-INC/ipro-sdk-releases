# IPRO SDK Releases

Download IPRO SDK Online Setup and the IPRO7 SDK Installation and Setup Guide (ISG), published by INTELPRO.

## Downloads

**[Download release v3.4.0](https://github.com/INTELPRO-INC/ipro-sdk-releases/releases/tag/v3.4.0)**

| File | Description |
| --- | --- |
| [IPRO-SDK-Online-Setup-3.4.0.zip](https://github.com/INTELPRO-INC/ipro-sdk-releases/releases/download/v3.4.0/IPRO-SDK-Online-Setup-3.4.0.zip) | Windows online installer; extract to obtain `IPRO-SDK-Online-Setup-3.4.0.exe`. |
| [IPRO7_ISG_V1.0_en.pdf](https://github.com/INTELPRO-INC/ipro-sdk-releases/releases/download/v3.4.0/IPRO7_ISG_V1.0_en.pdf) | English Installation and Setup Guide, version 1.0. |
| [SHA256SUMS.txt](https://github.com/INTELPRO-INC/ipro-sdk-releases/releases/download/v3.4.0/SHA256SUMS.txt) | SHA-256 checksums for the ZIP, PDF and extracted EXE. |

Use the files under **Assets** on the release page. GitHub's automatically generated source archives contain this repository's documentation, not the installer or SDK packages.

The online installer downloads selected packages from the official catalog and GitHub Releases. The ZIP does not contain a complete offline SDK. Customers do not need a GitHub account, token, SSH key, Git or GitHub CLI to install.

## Quick start

1. Download the installer ZIP and ISG PDF above. Verify the checksums, then extract the ZIP.
2. On Windows x64, run `IPRO-SDK-Online-Setup-3.4.0.exe`. Choose a writable installation directory that **does not yet exist**; keep your current working environment.
3. For a first installation, select **Full environment**. Choose package versions and review the resolved versions on the summary page.
4. Review the optional tasks. If you want VS Code to use the new environment immediately, select **Switch only selected component settings (backup created)**. Install VS Code if needed and keep the extension installation selected.
5. Complete installation and restart VS Code. Open the installed **SDK root**, which contains `CMakeLists.txt`, `apps` and `bsp`.
6. Run **IPRO Setup: Run Diagnostics**, then **IPRO: Select Project** and build an official example such as `ipro7ai_evk_demo` using **IPRO: Build**.

The wizard requires at least 5,000,000,000 bytes of space; allow additional room for downloads, builds and projects. The extension requires VS Code 1.102.0 or later. Network access to GitHub, `raw.githubusercontent.com` and release download redirects is required; optional VS Code downloads also require Microsoft download hosts.

## Installation options

| Mode | Package installed |
| --- | --- |
| Full environment | SDK, toolchain, build tools, IntelProCube, OpenOCD and extension |
| SDK only | SDK |
| Toolchain only | RISC-V toolchain |
| Build tools only | CMake, Ninja and Python |
| IntelProCube only | Flash tool |
| OpenOCD only | OpenOCD |
| Extension only | VS Code VSIX package |

Full installation resolves compatibility from the catalog. A single-package installation does not validate compatibility with your existing environment. Installer, SDK and extension versions are independent. `latest` means the catalog's selected stable/compatible version and may change over time.

For pinned versions, offline installation, editor configuration, flashing, serial monitoring, rollback and troubleshooting, follow the ISG. Offline installation requires a prepared cache with the matching package ZIP files and catalog, plus explicit `/OFFLINE=1`; the installer alone is insufficient.

## Verify downloads

In PowerShell, run the following in the download folder and compare the results with `SHA256SUMS.txt`:

```powershell
Get-FileHash -LiteralPath .\IPRO-SDK-Online-Setup-3.4.0.zip -Algorithm SHA256
Get-FileHash -LiteralPath .\IPRO7_ISG_V1.0_en.pdf -Algorithm SHA256
```

After extraction, verify the EXE:

```powershell
Get-FileHash -LiteralPath .\IPRO-SDK-Online-Setup-3.4.0.exe -Algorithm SHA256
```

Expected EXE size: **2,112,492 bytes**. Expected SHA-256:

```text
00a391b8b92a1bb7177607d6527c1875a77e91500fe2c816d8816e72381cdd18
```

The 3.4.0 EXE is not Authenticode signed. A matching checksum confirms file integrity, not a trusted publisher signature. Follow your organization's software policy; see ISG section 1.2.

## Troubleshooting and scope

Check `online-install.json` and `online-postinstall.log` inside the installation root, the adjacent `<install root>.download.log`, and the Setup log. The ISG describes how to collect diagnostics and resolve common issues.

This release targets Windows; the guide records Windows x64 testing. ARM64 hardware acceptance and general F5 single-step debugging are not established by this release. See ISG chapter 12 for the full verified scope.

## 中文說明

本 repository 提供 IPRO SDK 線上安裝器與 IPRO7 安裝設定指南的版本下載。請從上方 **Downloads** 或 **Releases → Assets** 下載 ZIP 與英文 ISG PDF，解壓縮後執行 EXE。首次安裝請選擇 **Full environment** 與尚不存在的新目錄，並依 ISG 完成 VS Code 設定及第一個範例建置。使用者下載及安裝不需要登入 GitHub；離線安裝須先備妥套件快取與 catalog。

## About

This repository distributes installer releases and setup documentation. Licensing for SDK components and third-party tools is governed by the terms included with those components.

[INTELPRO](https://www.intelpro.tw/) · [All releases](https://github.com/INTELPRO-INC/ipro-sdk-releases/releases)
