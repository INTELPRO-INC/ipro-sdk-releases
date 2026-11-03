# IPRO SDK Online Setup 3.6.0

修正 Windows 上建置 SDK 的問題，並加入 PowerShell 命令列流程。版本查詢方式與 3.5.0 相同，目前 latest 為 SDK 2.2.0。

- **修正 Windows 建置沒有使用 IPRO 工具鏈功能。**SDK 以 `/dev/null` 試編來辨識 IPRO 工具鏈，Windows 沒有這個路徑，因此所有 Windows 建置都改用標準 `-march` 旗標與非 DSP 版預編譯庫，結果與 macOS／Linux 建置不同。3.6.0 安裝 SDK 時改為在建置目錄中試編，建置記錄會出現 `Vendor toolchain detected`。
- 修正以 CMake 4 搭配 Ninja 建置時的 `rules.ninja: lexing error`（建置完成訊息帶有換行）。安裝器附的 CMake 3.30.5 不受影響，自行改用 CMake 4 時需要此修正。
- `tools\serial_monitor.py` 支援 Windows COM 埠，不需要 pyserial。
- 新增 `build_freertos.ps1`：在 PowerShell 建置、燒錄、開啟主控台與執行自動測試，動作與選項與 `build_freertos.sh` 相同。它會自動使用同一安裝目錄中的工具鏈、CMake、Ninja、Python 與 IntelProCube，不需要加入 PATH；IPRO7AI 板子的應用會自動帶入 `--chip-variant IPRO7AI` 與 2 MB 分割表。
- 以上修正套用於 Full 與 SDK-only 模式，只在 SDK 仍有該問題時套用，並在 SDK 根目錄寫入 `WINDOWS-HOST-FIXES.json`，記錄修改前後的 SHA-256。已內含修正的 SDK 不會被更動；原始 SDK 壓縮檔保留在下載快取。
- **SDK-only 與 Full 安裝出相同的 SDK。**SDK 一律依搭配的工具鏈準備：Full 用同時安裝的工具鏈，SDK-only 用 `/TOOLCHAINROOT=` 指定的、VS Code 設定的、最新的其他環境或 PATH 上的工具鏈。過去 SDK-only 保留原始預編譯庫，搭配 GCC 13.1.1 時 SDK 2.2.0 無法連結。
- **SDK 與工具鏈可以加到既有環境**：精靈列出之前安裝的環境及其內容，也可以用 `/ENV=` 靜默加裝。已有的檔案一律不覆蓋，同版本已存在時在選擇頁就提示；建置直接使用該環境的工具。
- 精靈改為先選套件與版本、再選安裝位置；完成頁列出 SDK 位置、命令列建置的第一個指令與工具鏈位置。

下載 EXE 或 ZIP 後依 `SHA256SUMS.txt` 核對。首次安裝選 Full environment 與新目錄；需要切換 VS Code 時勾選設定切換工作。舊環境保留，更新不會在背景覆蓋既有目錄。

本版在 Windows 11 x64 驗證完整安裝、SDK-only（新資料夾自動或指定配對工具鏈）、SDK 加到既有環境與同版本拒絕、離線模式與安裝精靈各頁，並在安裝後的環境以 VS Code Extension 的方式建置；以 `build_freertos.ps1` 建置、透過 IntelProCube 0.7.0 燒錄 IPRO7AI Reef EVK，看到開機訊息，單元測試 155 項 0 失敗。

工具鏈仍為 GCC 13.1.1：SDK 2.2.0 的 macOS／Linux 建置使用 GCC 14，Windows 建出的映像與其不同。安裝器未作 Authenticode 簽章；本版沒有 ARM64 實機驗證。詳見 `INSTALLATION.zh-TW.md`。

SDK 與工具仍取自 `INTELPRO-INC/IPRO-SDK`。此 Release 僅提供安裝包、校驗資訊與文件，不另行發布原始碼包。
