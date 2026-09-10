# IPRO SDK — Getting Started

Build, flash, and run your first application on an IntelPro IPRO board.

**[Download the latest installer](https://github.com/INTELPRO-INC/ipro-sdk-releases/releases/latest)** · **[SDK source](https://github.com/INTELPRO-INC/IPRO-SDK)** · **[Documentation](https://intelpro-inc.github.io/ipro-sdk-docs/)** · **[繁體中文](GETTING_STARTED.zh-TW.md)**

## Choose the right resource

| Resource | What you will find | Start here when… |
| --- | --- | --- |
| [IPRO-SDK](https://github.com/INTELPRO-INC/IPRO-SDK) | SDK source, application examples, board support, and SDK releases | You want to inspect code or work from a specific SDK revision. |
| [ipro-sdk-releases](https://github.com/INTELPRO-INC/ipro-sdk-releases) | Windows online installer, checksums, and installation guides | You are setting up a development PC or updating installed components. |
| [IPRO SDK documentation](https://intelpro-inc.github.io/ipro-sdk-docs/) | Tutorials, architecture, APIs, hardware, and troubleshooting | You want to learn the platform or develop an application. |

This repository distributes installers and documentation. SDK source remains in **IPRO-SDK**.

## 1. Prepare your PC and board

For the guided setup, use Windows 10/11 x64, an internet connection, and a writable installation directory. The installer checks for at least 5 GB free space; allow additional space for downloads and builds. VS Code 1.102.0 or later is required by the supplied extension and can be installed through the wizard.

For flashing and runtime checks, prepare the supported board for your example, a USB data cable, and the required UART connection or IPRO Debug Probe. You can install and compile without a board.

Customer installation does not require a GitHub account, token, Git, or GitHub CLI.

## 2. Install the development environment

1. Open the [latest installer release](https://github.com/INTELPRO-INC/ipro-sdk-releases/releases/latest). Download the **EXE**, or download the **ZIP** and extract it. Use the release assets; GitHub's automatic source archives contain only this repository's documents.
2. Compare the download hash with that release's `SHA256SUMS.txt`. For example:

   ```powershell
   Get-FileHash -LiteralPath .\IPRO-SDK-Online-Setup-3.5.0.exe -Algorithm SHA256
   ```

3. Run the EXE, select **Full environment**, and use a new directory, such as `C:\IPRO\versions\my-workstation`.
4. Keep each component on **latest** and review the resolved versions. Full installation includes the SDK, RISC-V toolchain, CMake/Ninja/Python, IntelProCube, OpenOCD, and VS Code extension.
5. Keep extension installation selected. Select **Install VS Code** if needed and **Switch only selected component settings** to configure VS Code for this environment.
6. Complete installation and restart VS Code.

Online Setup **3.5.0** currently discovers SDK **2.0.1**. Installer, SDK, extension, and tool versions are independent. The EXE is currently not Authenticode signed.

## 3. Open the SDK and build an example

1. In VS Code, choose **File → Open Folder** and open the installed SDK root. For SDK 2.0.1 this is `<installation directory>\sdk\IPRO-SDK-V2.0.1`; it contains `CMakeLists.txt`, `apps/`, and `bsp/`.
2. Open the Command Palette with **Ctrl+Shift+P** and run **IPRO Setup: Run Diagnostics**. Check that the SDK, compiler, build tools, and flash tool are found. If necessary, run **IPRO Setup: Set SDK Root**.
3. Run **IPRO: Select Project**. For an IPRO7AI EVK, select `ipro7ai_evk_demo` under `apps/platform/`. Use an example and board configuration matching your hardware.
4. Run **IPRO: Build** and wait for successful completion.

For this example, firmware output is under:

```text
apps/platform/ipro7ai_evk_demo/build/build_out/
  ipro7ai_evk_demo_IPRO7.bin
  ipro7ai_evk_demo_IPRO7.elf
```

You have completed the first build when the build succeeds and the firmware files exist.

## 4. Flash and monitor

1. Connect the board following its [hardware documentation](https://intelpro-inc.github.io/ipro-sdk-docs/hardware), including its download/boot requirements.
2. Run **IPRO: Select Serial Port** and select the board's UART. With an IPRO Debug Probe, use its UART interface for flashing and console output.
3. Check the chip and flash parameters with **IPRO Config: Select Flash Target** and **IPRO Config: Configure Flash…**. Close other programs using the same port.
4. Run **IPRO: Flash** to replace the board firmware with the built image.
5. Run **IPRO: Serial Monitor**. Use **IPRO Config: Set Monitor Baud Rate** to match the example's console configuration, and check its startup output.

Once these steps work individually, **IPRO: Build, Flash & Monitor** runs them in sequence. Flashing speed and console baud rate are separate settings.

## 5. Keep using the same installer

Run the same **3.5.0 EXE** again whenever you need an update. Every online installation refreshes the available versions:

- **SDK:** discovers the newest official `IPRO-SDK-Vx.y.z` release with a supported SDK archive and SHA-256 metadata.
- **Tools and extension:** follow each component's published stable version in the live package catalog.

Choose Full environment or one of the six individual component modes. Existing installation directories are retained; choose a new directory and explicitly switch settings when ready. An exact version can be selected instead of `latest`.

Maintainers publish tool updates through the package manager and update the component's stable pointer. New package content does **not** require rebuilding the installer. See [3.5.0 installation and publishing rules](INSTALLATION.zh-TW.md) for naming, compatibility, pinned versions, and offline use.

Full mode prepares affected SDK libraries for the older Windows linker. SDK-only preserves the original libraries and may need a newer toolchain. Compatibility checks apply where package dependencies are declared.

## Explore the SDK

- [Getting started and environment setup](https://intelpro-inc.github.io/ipro-sdk-docs/getting-started)
- [VS Code workflow](https://intelpro-inc.github.io/ipro-sdk-docs/vscode-extension)
- [Application examples](https://intelpro-inc.github.io/ipro-sdk-docs/applications)
- [SDK architecture](https://intelpro-inc.github.io/ipro-sdk-docs/architecture)
- [API reference](https://intelpro-inc.github.io/ipro-sdk-docs/api-reference)
- [BLE development](https://intelpro-inc.github.io/ipro-sdk-docs/ble-development)
- [FAQ](https://intelpro-inc.github.io/ipro-sdk-docs/faq)

For command-line development on other hosts, follow the [SDK repository's build instructions](https://github.com/INTELPRO-INC/IPRO-SDK#quick-start) and obtain the appropriate host toolchain. The Windows installer is not a Linux or macOS installer. Documentation chapters may describe earlier versions; use the current release notes for installer behavior.

## Troubleshooting

| Symptom | First check |
| --- | --- |
| SDK or tools not found | Open the SDK root, rerun Diagnostics, and check the selected tool paths. |
| Download or version lookup fails | Check access to GitHub, its API, raw content, and release download hosts; retry after an API rate limit clears. |
| Serial port unavailable | Check the USB cable, board connection, driver, and other applications using the port. |
| Console text is unreadable | Match the monitor baud rate to the firmware; it is separate from flash speed. |
| SDK-only build reports unsupported relocation | Use Full environment or a compatible newer linker; see the 3.5.0 installation notes. |

Keep `online-install.json`, `online-postinstall.log`, and the adjacent `<installation directory>.download.log` when reporting an installation issue. Include the board, SDK/tool versions, and relevant build output for development issues.

Windows x64 installation and example compilation are verified for 3.5.0. This release does not add ARM64 hardware or board-flashing acceptance tests. SDK and third-party component licensing follows the notices supplied with each component.
