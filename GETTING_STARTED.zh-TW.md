# IPRO SDK 快速入門

[English](README.md) · [下載最新安裝包](https://github.com/INTELPRO-INC/ipro-sdk-releases/releases/latest) · [線上文件](https://intelpro-inc.github.io/ipro-sdk-docs/)

這份指南帶你完成開發環境安裝、第一個專案編譯，以及上板燒錄與串口監控。

## 三個公開入口

| 入口 | 用途 |
| --- | --- |
| [IPRO-SDK](https://github.com/INTELPRO-INC/IPRO-SDK) | SDK 原始碼、應用範例、板級支援與 SDK 正式版本。 |
| [ipro-sdk-releases](https://github.com/INTELPRO-INC/ipro-sdk-releases) | Windows 線上安裝包、SHA-256 校驗碼及安裝說明。 |
| [IPRO SDK 文件網站](https://intelpro-inc.github.io/ipro-sdk-docs/) | 快速開始、教學、架構、API、硬體與常見問題。 |

第一次使用請先從安裝包開始；需要閱讀或修改 SDK 程式碼時前往 IPRO-SDK。下載倉庫只放安裝包與文件，SDK 原始碼仍由 IPRO-SDK 倉庫提供。

## 1. 準備環境

使用 Windows 10/11 x64、可連線 GitHub 的網路，以及可寫入的新安裝目錄。安裝器要求至少 5 GB 可用空間，另需預留下載快取與編譯空間。VS Code 需為 1.102.0 以上，可由安裝器協助安裝。

安裝與編譯不需要接板。燒錄和執行範例時，請準備相符的開發板、USB 資料線，以及板卡要求的 UART 或 IPRO Debug Probe 連接。下載安裝不需要 GitHub 帳號、Token、Git 或 GitHub CLI。

## 2. 安裝 SDK 與工具

1. 開啟[最新安裝包 Release](https://github.com/INTELPRO-INC/ipro-sdk-releases/releases/latest)，下載 EXE，或下載 ZIP 後解壓縮。請使用 Assets 中的安裝包。
2. 使用同一 Release 的 `SHA256SUMS.txt` 核對下載檔，例如：

   ```powershell
   Get-FileHash -LiteralPath .\IPRO-SDK-Online-Setup-3.5.0.exe -Algorithm SHA256
   ```

3. 執行 EXE，選 **Full environment**，指定尚不存在的新目錄，例如 `C:\IPRO\versions\my-workstation`。
4. 各元件保留 **latest**，確認摘要頁的 SDK 與工具版本。Full 包含 SDK、RISC-V toolchain、CMake/Ninja/Python、IntelProCube、OpenOCD 及 VS Code Extension。
5. 保留 Extension 安裝工作；需要時勾選安裝 VS Code。要立即切換到新環境，勾選 **Switch only selected component settings**。
6. 安裝完成後重新啟動 VS Code。

安裝器 3.5.0 目前可取得 SDK 2.0.1；安裝器、SDK、工具及 Extension 各有自己的版本號。EXE 目前未作 Authenticode 簽章。

## 3. 編譯第一個專案

1. 在 VS Code 用 **File → Open Folder** 開啟 SDK 根目錄。2.0.1 的路徑是 `<安裝目錄>\sdk\IPRO-SDK-V2.0.1`，裡面應有 `CMakeLists.txt`、`apps/`、`bsp/`。
2. 按 **Ctrl+Shift+P**，執行 **IPRO Setup: Run Diagnostics**，確認 SDK、編譯器、建置工具與燒錄工具均可找到。需要時以 **IPRO Setup: Set SDK Root** 指定 SDK。
3. 執行 **IPRO: Select Project**。若使用 IPRO7AI EVK，可選 `apps/platform/ipro7ai_evk_demo`；其他板卡請選相符的範例與設定。
4. 執行 **IPRO: Build**，等候成功完成。

本範例的輸出位於 `apps/platform/ipro7ai_evk_demo/build/build_out/`，包含 `ipro7ai_evk_demo_IPRO7.bin` 與 `.elf`。建置成功且找到輸出檔，即完成第一個編譯。

## 4. 燒錄與監控

1. 依[硬體文件](https://intelpro-inc.github.io/ipro-sdk-docs/hardware)接線，確認板卡的下載／啟動模式。
2. 執行 **IPRO: Select Serial Port** 選擇板卡 UART。使用 IPRO Debug Probe 時，燒錄與 Monitor 使用 UART 介面。
3. 用 **IPRO Config: Select Flash Target** 與 **IPRO Config: Configure Flash…** 確認晶片及 Flash 參數，關閉占用相同串口的其他程式。
4. 執行 **IPRO: Flash**，將編譯好的映像寫入板卡；此步驟會取代板上韌體。
5. 執行 **IPRO: Serial Monitor**，以 **IPRO Config: Set Monitor Baud Rate** 設定符合範例的 console 速率，確認開機輸出。

各步驟成功後，可改用 **IPRO: Build, Flash & Monitor** 依序執行。燒錄速率與 Monitor 速率是不同設定。

## 5. 之後如何更新

需要更新時，重新執行**同一支 3.5.0 EXE**。每次線上安裝都會查詢版本：

- SDK：自動找到符合命名、壓縮格式與 SHA-256 要求的最新正式 SDK Release。
- 工具與 Extension：取得正式套件清單中各元件的最新 stable 版本。

可選 Full，或只更新 SDK、toolchain、build tools、IntelProCube、OpenOCD、Extension。安裝到新目錄後再切換設定，舊環境會保留；也能指定舊版而不使用 `latest`。

維護者透過套件管理中心發布工具並更新 stable 後，客戶再次使用同一支 EXE 即可取得，**不需重製安裝包**。SDK 正式發布與工具登錄規則、相依性及離線選项詳見[3.5.0 安裝說明](INSTALLATION.zh-TW.md)。此機制不會在背景覆蓋既有工作環境。

Full 模式包含舊 Windows linker 的 SDK 預編譯庫相容處理；SDK-only 保留原始庫，與舊工具鏈搭配時可能需另行更新工具鏈。

## 接著閱讀

- [快速開始](https://intelpro-inc.github.io/ipro-sdk-docs/getting-started)：環境與第一個專案。
- [VS Code Extension](https://intelpro-inc.github.io/ipro-sdk-docs/vscode-extension)：控制中心與開發流程。
- [應用範例](https://intelpro-inc.github.io/ipro-sdk-docs/applications)：尋找接近產品需求的起點。
- [SDK 架構](https://intelpro-inc.github.io/ipro-sdk-docs/architecture)與 [API 參考](https://intelpro-inc.github.io/ipro-sdk-docs/api-reference)：了解模組與介面。
- [FAQ](https://intelpro-inc.github.io/ipro-sdk-docs/faq)：常見問題。

Linux／macOS 命令列開發請依 [SDK README](https://github.com/INTELPRO-INC/IPRO-SDK#quick-start) 準備相應工具鏈；Windows 安裝器不適用其他主機系統。網站部分章節可能描述較早版本，安裝器行為請以目前 Release 說明為準。

若發生安裝問題，保留 `online-install.json`、`online-postinstall.log` 及相鄰的 `<安裝目錄>.download.log`。回報編譯／上板問題時附上板卡、SDK 與工具版本、相關訊息。3.5.0 已驗證 Windows x64 安裝與範例編譯，本次沒有新增 ARM64 實機或板上燒錄驗證。
