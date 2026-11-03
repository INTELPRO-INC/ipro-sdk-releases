# IPRO SDK — Getting Started

Build, flash, and run your first application on an INTELPRO IPRO board.

**[Download the latest installer](https://github.com/INTELPRO-INC/ipro-sdk-releases/releases/latest)** · **[SDK source](https://github.com/INTELPRO-INC/IPRO-SDK)** · **[Documentation](https://intelpro-inc.github.io/ipro-sdk-docs/en/)** · **[繁體中文](GETTING_STARTED.zh-TW.md)**

## Choose the right resource

| Resource | What you will find | Start here when… |
| --- | --- | --- |
| [IPRO-SDK](https://github.com/INTELPRO-INC/IPRO-SDK) | SDK source, application examples, board support, and SDK releases | You want to inspect code or work from a specific SDK revision. |
| [ipro-sdk-releases](https://github.com/INTELPRO-INC/ipro-sdk-releases) | Windows online installer, checksums, and installation guides | You are setting up a development PC or updating installed components. |
| [IPRO SDK documentation](https://intelpro-inc.github.io/ipro-sdk-docs/en/) | Tutorials, architecture, APIs, hardware, and troubleshooting | You want to learn the platform or develop an application. |

This repository distributes installers and documentation. SDK source remains in **IPRO-SDK**.

## 1. Prepare your PC and board

For the guided setup, use Windows 10/11 x64, an internet connection, and a writable installation directory. The installer checks for at least 5 GB free space; allow additional space for downloads and builds. VS Code 1.102.0 or later is required by the supplied extension and can be installed through the wizard.

The examples below use the IPRO7AI Reef EVK with its IPRO Debug Probe and a USB-C data cable. You can install and compile without a board.

Customer installation does not require a GitHub account, token, Git, or GitHub CLI.

## 2. Install the development environment

1. Open the [latest installer release](https://github.com/INTELPRO-INC/ipro-sdk-releases/releases/latest). Download the **EXE**, or download the **ZIP** and extract it. Use the release assets; GitHub's automatic source archives contain only this repository's documents.
2. Compare the download hash with that release's `SHA256SUMS.txt`:

   ```powershell
   Get-FileHash -LiteralPath .\IPRO-SDK-Online-Setup-3.6.0.exe -Algorithm SHA256
   ```

   The `Hash` column must equal the value listed for the same file (case does not matter).

3. Run the EXE and select **Full environment**.
4. Keep each component on **latest**. The list shows what latest resolves to, for example `latest (2.2.0)`. Full installation includes the SDK, RISC-V toolchain, CMake/Ninja/Python, IntelProCube, OpenOCD, and VS Code extension.
5. Use a new directory, such as the default `C:\IPRO\versions\3.6.0-<date-time>`.
6. Keep extension installation selected. Select **Install VS Code** if needed and **Switch only selected component settings** to configure VS Code for this environment.
7. Complete installation. The finished page shows the SDK folder, the first command-line command, and the toolchain folder. Restart VS Code.

Online Setup **3.6.0** currently discovers SDK **2.2.0** and installs the Windows RISC-V toolchain GCC 13.1.1. It prepares the SDK for that toolchain and fixes the SDK's Windows build issues; the changes are recorded in `PREBUILT-ARCHIVES-STRIPPED.json` and `WINDOWS-HOST-FIXES.json` in the SDK root. Installer, SDK, extension, and tool versions are independent. The EXE is currently not Authenticode signed.

## 3. Open the SDK and build an example

### In VS Code

1. Choose **File → Open Folder** and open the installed SDK root, `<installation directory>\sdk\IPRO-SDK-V2.2.0`. It contains `CMakeLists.txt`, `apps/`, and `bsp/`.
2. Open the Command Palette with **Ctrl+Shift+P** and run **IPRO Setup: Run Diagnostics**. Check that the SDK, compiler, build tools, and flash tool are found. If necessary, run **IPRO Setup: Set SDK Root**.
3. Run **IPRO: Select Project** and select `ipro7ai_reef_demo` under `apps/platform/`.
4. Run **IPRO: Build** and wait for successful completion.

### From PowerShell

The SDK includes `build_freertos.ps1`, the Windows counterpart of `build_freertos.sh`. It uses the toolchain, CMake, Ninja, Python, and IntelProCube of the same installation, whether or not they are on PATH:

```powershell
cd <installation directory>\sdk\IPRO-SDK-V2.2.0
powershell -ExecutionPolicy Bypass -File .\build_freertos.ps1 build ipro7ai_reef_demo
```

The last lines must be:

```text
[OK] Build succeeded (21s)
  Binary: ...\apps\platform\ipro7ai_reef_demo\build\build_out\ipro7ai_reef_demo_IPRO7.bin (219 KB)
```

`-ExecutionPolicy Bypass` applies to this one run and does not change system settings. Run `.\build_freertos.ps1 -h` for all actions and options.

Either way, the firmware output is:

```text
apps/platform/ipro7ai_reef_demo/build/build_out/
  ipro7ai_reef_demo_IPRO7.bin
  ipro7ai_reef_demo_IPRO7.elf
```

## 4. Flash and monitor

### In VS Code

1. Connect the board following its [hardware documentation](https://intelpro-inc.github.io/ipro-sdk-docs/en/hardware/boards/reef-evk/).
2. Run **IPRO: Select Serial Port** and select the IPRO Debug Probe's UART interface, used for both flashing and console output.
3. In **IPRO Config: Configure Flash…** set the chip variant to `IPRO7AI` and the partition table to `partition_cfg_2M.toml`. Close other programs using the same port.
4. Run **IPRO: Flash** to replace the board firmware with the built image.
5. Run **IPRO: Serial Monitor** at 115200 baud and check the startup output `IPRO7AI Reef carrier demo`.

Once these steps work individually, **IPRO: Build, Flash & Monitor** runs them in sequence. Flashing speed and console baud rate are separate settings.

### From PowerShell

```powershell
powershell -ExecutionPolicy Bypass -File .\build_freertos.ps1 flash monitor ipro7ai_reef_demo -w "IPRO7AI Reef carrier demo"
```

The script picks the probe's UART port, IntelProCube enters download mode, flashes and resets the board, and the monitor stops when the banner appears:

```text
[All Success]
[OK] Flash succeeded (6s)
...
[serial_monitor] MATCHED: IPRO7AI Reef carrier demo
```

Pass `-p COM5` when several serial ports are connected. IPRO7AI board applications get `--chip-variant IPRO7AI` and the 2 MB partition table automatically.

## 5. Keep using the same installer

Run the same **3.6.0 EXE** again whenever you need an update. Every online installation refreshes the available versions:

- **SDK:** discovers the newest official `IPRO-SDK-Vx.y.z` release with a supported SDK archive and SHA-256 metadata.
- **Tools and extension:** follow each component's published stable version in the live package catalog.

Choose Full environment or one of the six individual component modes. With **SDK only** or **Toolchain only** you can add the package to an environment you installed before: the wizard lists those environments with their SDKs and toolchains, nothing already installed is overwritten, and a version that is already there is refused. Silent installs use `/ENV=<environment folder>`. An exact version can be selected instead of `latest`.

The SDK is prepared for the toolchain it will be built with in every mode: the toolchain installed with it, the toolchains of the environment it joins, or for an SDK-only install into a new directory the toolchain given with `/TOOLCHAINROOT=`, configured in VS Code, or of the newest other environment. See the [3.6.0 installation notes](INSTALLATION.zh-TW.md) for pinned versions, offline use, and the publishing rules.

## Explore the SDK

- [Get started](https://intelpro-inc.github.io/ipro-sdk-docs/en/get-started/) and [Windows installation](https://intelpro-inc.github.io/ipro-sdk-docs/en/get-started/windows/)
- [VS Code extension](https://intelpro-inc.github.io/ipro-sdk-docs/en/guides/vscode/)
- [Application examples](https://intelpro-inc.github.io/ipro-sdk-docs/en/examples/)
- [SDK architecture](https://intelpro-inc.github.io/ipro-sdk-docs/en/guides/architecture/)
- [API reference](https://intelpro-inc.github.io/ipro-sdk-docs/en/reference/api/)
- [BLE development](https://intelpro-inc.github.io/ipro-sdk-docs/en/guides/ble/)
- [FAQ](https://intelpro-inc.github.io/ipro-sdk-docs/en/reference/faq/)

For command-line development on macOS or Linux, follow the [SDK repository's build instructions](https://github.com/INTELPRO-INC/IPRO-SDK#quick-start) and obtain the appropriate host toolchain. The Windows installer is not a Linux or macOS installer.

## Troubleshooting

| Symptom | First check |
| --- | --- |
| SDK or tools not found | Open the SDK root, rerun Diagnostics, and check the selected tool paths. |
| Download or version lookup fails | Check access to GitHub, its API, raw content, and release download hosts; retry after an API rate limit clears. |
| Serial port unavailable | Check the USB cable, board connection, driver, and other applications using the port. |
| Console text is unreadable | Match the monitor baud rate to the firmware; it is separate from flash speed. |
| The finished page says no toolchain was found | The SDK-only install found no toolchain to prepare the SDK for. Add the SDK to an environment that has one, or run Setup again with `/TOOLCHAINROOT=<toolchain folder>`. |
| `.\build_freertos.ps1` is blocked | Run it as `powershell -ExecutionPolicy Bypass -File .\build_freertos.ps1 ...`. |

Keep `online-install.json`, `online-postinstall.log`, and the adjacent `<installation directory>.download.log` when reporting an installation issue; additions to an existing environment write theirs under `online-install.d\`. Include the board, SDK/tool versions, and relevant build output for development issues.

Online Setup 3.6.0 is verified on Windows x64: full, SDK-only, and add-to-environment installs, builds the way the VS Code extension runs them (CMake with Ninja) and with `build_freertos.ps1`, and flashing an IPRO7AI Reef EVK with `build_freertos.ps1`. ARM64 hardware is not tested. SDK and third-party component licensing follows the notices supplied with each component.
