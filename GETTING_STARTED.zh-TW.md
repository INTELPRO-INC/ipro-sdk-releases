# IPRO SDK 快速入門

[English](README.md) · [下載最新安裝包](https://github.com/INTELPRO-INC/ipro-sdk-releases/releases/latest) · [線上文件](https://intelpro-inc.github.io/ipro-sdk-docs/)

這份指南帶你完成開發環境安裝、第一個專案編譯，以及上板燒錄與主控台。

## 三個公開入口

| 入口 | 用途 |
| --- | --- |
| [IPRO-SDK](https://github.com/INTELPRO-INC/IPRO-SDK) | SDK 原始碼、應用範例、板級支援與 SDK 正式版本。 |
| [ipro-sdk-releases](https://github.com/INTELPRO-INC/ipro-sdk-releases) | Windows 線上安裝包、SHA-256 校驗碼及安裝說明。 |
| [IPRO SDK 文件網站](https://intelpro-inc.github.io/ipro-sdk-docs/) | 快速開始、教學、架構、API、硬體與常見問題。 |

第一次使用請先從安裝包開始；需要閱讀或修改 SDK 程式碼時前往 IPRO-SDK。下載倉庫只放安裝包與文件，SDK 原始碼仍由 IPRO-SDK 倉庫提供。

## 1. 準備環境

使用 Windows 10/11 x64、可連線 GitHub 的網路，以及可寫入的新安裝目錄。安裝器要求至少 5 GB 可用空間，另需預留下載快取與編譯空間。VS Code 需為 1.102.0 以上，可由安裝器協助安裝。

以下範例使用 IPRO7AI Reef EVK、板上的 IPRO Debug Probe 與 USB-C 資料線。安裝與編譯不需要接板。下載安裝不需要 GitHub 帳號、Token、Git 或 GitHub CLI。

## 2. 安裝 SDK 與工具

1. 開啟[最新安裝包 Release](https://github.com/INTELPRO-INC/ipro-sdk-releases/releases/latest)，下載 EXE，或下載 ZIP 後解壓縮。請使用 Assets 中的安裝包。
2. 使用同一 Release 的 `SHA256SUMS.txt` 核對下載檔：

   ```powershell
   Get-FileHash -LiteralPath .\IPRO-SDK-Online-Setup-3.6.0.exe -Algorithm SHA256
   ```

   `Hash` 欄應與清單中同名檔案的值相同（大小寫不影響）。

3. 執行 EXE，選 **Full environment**。
4. 各元件保留 **latest**，選單會直接顯示對應的版本，例如 `latest (2.2.0)`。Full 包含 SDK、RISC-V toolchain、CMake/Ninja/Python、IntelProCube、OpenOCD 及 VS Code Extension。
5. 目的資料夾使用尚不存在的新目錄，例如預設的 `C:\IPRO\versions\3.6.0-<日期時間>`。
6. 保留 Extension 安裝工作；需要時勾選安裝 VS Code。要立即切換到新環境，勾選 **Switch only selected component settings**。
7. 安裝完成頁會列出 SDK 位置、命令列建置的第一個指令與工具鏈位置。完成後重新啟動 VS Code。

安裝器 3.6.0 目前取得 SDK 2.2.0，並安裝 Windows 版 RISC-V 工具鏈 GCC 13.1.1。它會依這套工具鏈準備 SDK，並修正 SDK 在 Windows 上的建置問題，處理內容記錄在 SDK 根目錄的 `PREBUILT-ARCHIVES-STRIPPED.json` 與 `WINDOWS-HOST-FIXES.json`。安裝器、SDK、工具及 Extension 各有自己的版本號。EXE 目前未作 Authenticode 簽章。

## 3. 編譯第一個專案

### 在 VS Code

1. 用 **File → Open Folder** 開啟 SDK 根目錄 `<安裝目錄>\sdk\IPRO-SDK-V2.2.0`，裡面應有 `CMakeLists.txt`、`apps/`、`bsp/`。
2. 按 **Ctrl+Shift+P**，執行 **IPRO Setup: Run Diagnostics**，確認 SDK、編譯器、建置工具與燒錄工具均可找到。需要時以 **IPRO Setup: Set SDK Root** 指定 SDK。
3. 執行 **IPRO: Select Project**，選 `apps/platform/ipro7ai_reef_demo`。
4. 執行 **IPRO: Build**，等候成功完成。

### 在 PowerShell

SDK 附有 `build_freertos.ps1`（`build_freertos.sh` 的 Windows 版），會使用同一安裝目錄中的工具鏈、CMake、Ninja、Python 與 IntelProCube，不需要加入 PATH：

```powershell
cd <安裝目錄>\sdk\IPRO-SDK-V2.2.0
powershell -ExecutionPolicy Bypass -File .\build_freertos.ps1 build ipro7ai_reef_demo
```

最後應看到：

```text
[OK] Build succeeded (21s)
  Binary: ...\apps\platform\ipro7ai_reef_demo\build\build_out\ipro7ai_reef_demo_IPRO7.bin (219 KB)
```

`-ExecutionPolicy Bypass` 只影響這一次執行，不會改變系統設定。`.\build_freertos.ps1 -h` 列出所有動作與選項。

兩種方式的輸出都位於 `apps/platform/ipro7ai_reef_demo/build/build_out/`，包含 `ipro7ai_reef_demo_IPRO7.bin` 與 `.elf`。

## 4. 燒錄與主控台

### 在 VS Code

1. 依[開發板文件](https://intelpro-inc.github.io/ipro-sdk-docs/hardware/boards/reef-evk/)接線。
2. 執行 **IPRO: Select Serial Port**，選 IPRO Debug Probe 的 UART 介面；燒錄與主控台都使用它。
3. 在 **IPRO Config: Configure Flash…** 把晶片配置設為 `IPRO7AI`、分割表設為 `partition_cfg_2M.toml`，並關閉占用同一個埠的其他程式。
4. 執行 **IPRO: Flash**，將編譯好的映像寫入板卡；此步驟會取代板上韌體。
5. 執行 **IPRO: Serial Monitor**（115200），確認開機訊息 `IPRO7AI Reef carrier demo`。

各步驟成功後，可改用 **IPRO: Build, Flash & Monitor** 依序執行。燒錄速率與主控台速率是不同設定。

### 在 PowerShell

```powershell
powershell -ExecutionPolicy Bypass -File .\build_freertos.ps1 flash monitor ipro7ai_reef_demo -w "IPRO7AI Reef carrier demo"
```

腳本會選用 probe 的 UART 埠，由 IntelProCube 進入下載模式、燒錄並重置板子，看到開機訊息後主控台結束：

```text
[All Success]
[OK] Flash succeeded (6s)
...
[serial_monitor] MATCHED: IPRO7AI Reef carrier demo
```

接了多個序列埠時以 `-p COM5` 指定。IPRO7AI 板子的應用會自動帶入 `--chip-variant IPRO7AI` 與 2 MB 分割表。

## 5. 之後如何更新

需要更新時，重新執行**同一支 3.6.0 EXE**。每次線上安裝都會查詢版本：

- SDK：自動找到符合命名、壓縮格式與 SHA-256 要求的最新正式 SDK Release。
- 工具與 Extension：取得正式套件清單中各元件的最新 stable 版本。

可選 Full，或只更新 SDK、toolchain、build tools、IntelProCube、OpenOCD、Extension。選 **SDK only** 或 **Toolchain only** 時，可以加到之前安裝的環境：精靈會列出各環境與其中的 SDK、工具鏈，已有的檔案一律不覆蓋，同版本已存在時會提示。靜默安裝用 `/ENV=<環境資料夾>`。也能指定舊版而不使用 `latest`。

不論哪種模式，SDK 都依搭配的工具鏈準備：同時安裝的工具鏈、加入環境中的工具鏈，或 SDK 單獨裝到新資料夾時以 `/TOOLCHAINROOT=` 指定、VS Code 設定的、最新的其他環境中的工具鏈。指定版本、離線使用與發布規則詳見[3.6.0 安裝說明](INSTALLATION.zh-TW.md)。此機制不會在背景覆蓋既有工作環境。

## 接著閱讀

- [快速開始](https://intelpro-inc.github.io/ipro-sdk-docs/get-started/)與 [Windows 安裝](https://intelpro-inc.github.io/ipro-sdk-docs/get-started/windows/)
- [VS Code Extension](https://intelpro-inc.github.io/ipro-sdk-docs/guides/vscode/)
- [應用範例](https://intelpro-inc.github.io/ipro-sdk-docs/examples/)：尋找接近產品需求的起點。
- [SDK 架構](https://intelpro-inc.github.io/ipro-sdk-docs/guides/architecture/)與 [API 參考](https://intelpro-inc.github.io/ipro-sdk-docs/reference/api/)
- [常見問題](https://intelpro-inc.github.io/ipro-sdk-docs/reference/faq/)

macOS／Linux 命令列開發請依 [SDK README](https://github.com/INTELPRO-INC/IPRO-SDK#quick-start) 準備相應工具鏈；Windows 安裝器不適用其他主機系統。

若發生安裝問題，保留 `online-install.json`、`online-postinstall.log` 及相鄰的 `<安裝目錄>.download.log`；加到既有環境時，記錄在該環境的 `online-install.d\`。安裝完成頁若提示找不到工具鏈，請把 SDK 加到有工具鏈的環境，或以 `/TOOLCHAINROOT=` 重新安裝。回報編譯／上板問題時附上板卡、SDK 與工具版本、相關訊息。

3.6.0 在 Windows x64 驗證完整安裝、SDK-only 與加到既有環境，照 VS Code Extension 的方式（CMake + Ninja）及以 `build_freertos.ps1` 建置，並以 `build_freertos.ps1` 燒錄 IPRO7AI Reef EVK；沒有 ARM64 實機驗證。
