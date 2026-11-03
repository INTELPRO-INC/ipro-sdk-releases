# 動態線上安裝器 3.6.0

同一支 `IPRO-SDK-Online-Setup-3.6.0.exe` 每次執行安裝時都會重新查詢版本。SDK 或工具內容更新後，客戶不必重新下載或重新產生安裝程式。3.6.0 另外修正安裝後 SDK 在 Windows 上的建置問題，並提供 PowerShell 命令列流程。

## 下載與安裝

正式下載位置：<https://github.com/INTELPRO-INC/ipro-sdk-releases/releases/tag/v3.6.0>。

1. 下載 EXE，或下載 ZIP 後解壓縮。
2. 在 PowerShell 核對雜湊值：

    ```powershell
    Get-FileHash .\IPRO-SDK-Online-Setup-3.6.0.exe -Algorithm SHA256
    ```

    `Hash` 欄應與 `SHA256SUMS.txt` 中同名檔案的值相同（大小寫不影響）。

3. 執行 EXE。Windows 顯示 SmartScreen 警告時，依公司的資訊安全規定處理（安裝器未作程式碼簽章）。
4. **Choose packages** 選 **Full environment**。
5. **Select package versions** 各元件保持 `latest`，選單會直接顯示目前對應的版本，例如 `latest (2.2.0)`。
6. **Select Destination Location** 保留預設的新資料夾 `C:\IPRO\versions\3.6.0-<日期時間>`，或填入一個尚不存在的資料夾。
7. 需要切換 VS Code 設定時，勾選設定切換工作；要在一般終端機直接執行工具時，勾選加入 PATH。
8. 安裝完成頁會列出 SDK 的位置、命令列建置的第一個指令與工具鏈的位置。重新開啟 VS Code 與終端機。

安裝器也支援 SDK、toolchain、build tools、IntelProCube、OpenOCD、Extension 六種單獨安裝模式，SDK 與 toolchain 可以加到既有環境，見下一節。已安裝的檔案一律不覆蓋；此功能是執行安裝時抓取更新，不是在背景修改目前使用中的 SDK。

## 單獨安裝 SDK 或工具鏈

選 **SDK only** 或 **Toolchain only** 時，**Select package versions** 之後會出現 **Choose where to install**：

- **Add to this existing environment**：清單列出之前以 Online Setup 安裝的環境（`C:\IPRO\versions` 與目的資料夾所在的資料夾），並顯示各環境已有的 SDK 與工具鏈，預設選最新、且有工具鏈的環境。SDK 會放進該環境的 `sdk\<SDK 名稱>`，工具鏈放進 `toolchain\<名稱>`，直接使用環境中已有的工具建置。所選版本已在該環境時，按 **Next** 會提示改選版本或改裝到新資料夾。
- **A new directory**：裝到新的資料夾。

不論哪一種，SDK 都會依搭配的工具鏈準備（見「Windows 工具鏈相容處理」一節），結果與 Full 模式相同：

| 安裝方式 | SDK 依哪一套工具鏈準備 |
| --- | --- |
| Full environment | 同時安裝的工具鏈 |
| SDK 加到既有環境 | 該環境中的工具鏈 |
| SDK 裝到新資料夾 | 依序找：`/TOOLCHAINROOT=` 指定的資料夾、VS Code 設定 `ipro.toolchainPath`、同一層資料夾中最新的其他 Online Setup 環境、PATH 上的 `riscv64-unknown-elf-ld.exe` |

SDK 裝到新資料夾時，配對到的工具鏈記錄在該資料夾 `online-install.json` 的 `pairedToolchain`；`build_freertos.ps1` 會使用它，以及它所在環境的 CMake、Ninja、Python 與 IntelProCube。完全找不到工具鏈時，預編譯庫維持原樣，完成頁會提示改用 `/TOOLCHAINROOT=` 或先安裝工具鏈。

加到既有環境不會改動該環境原有的檔案、`online-install.json` 與 `installer\` 內容；每次加裝另寫一份記錄到 `online-install.d\<日期時間>-<模式>.json`。工具鏈加到既有環境時，原有的 SDK 不會被修改；若它們需要舊 linker 相容處理，記錄的 `warnings` 會提醒你把該 SDK 重新加裝到此環境。

## 版本從哪裡取得

| 元件 | 每次執行時查詢的來源 |
| --- | --- |
| SDK | `INTELPRO-INC/IPRO-SDK` 正式 Release 與 `packages/index.json` |
| Toolchain、CMake/Ninja/Python、IntelProCube、OpenOCD、Extension | 同一 SDK 倉庫 `main/packages/index.json` 中各元件的 stable 指標 |

SDK 自動辨識非草稿、非預覽版的 `IPRO-SDK-V<主版.次版.修訂版>`，按版本數值比較，不受整個 GitHub 倉庫的 Latest 標記或發布順序影響。辨識 `IPRO-SDK-V<版本>.tar.gz` 或 `.zip`，以 GitHub asset 的 SHA-256 與大小驗證下載，並核對解壓後的 `VERSION.txt`。

清單中已登錄的同版 SDK 優先保留其相依性資訊；完整模式檢查已宣告的工具版本要求。直接從 Release 發現、尚未登錄相依性的 SDK 使用目前工具 stable 版本，不代表已驗證所有未來 SDK／工具組合。

## Windows 主機修正

Full 與 SDK-only 模式安裝 SDK 時，3.6.0 會檢查並修正下列 Windows 問題。每一項只在 SDK 仍有該問題時套用；已內含修正的 SDK 版本不會被更動。

| 檔案 | 修正內容 |
| --- | --- |
| `cmake\toolchain.cmake` | 以建置目錄中的檔案試編來辨識 IPRO 工具鏈。原本使用 `/dev/null`，Windows 沒有此路徑，建置因此改用標準 `-march` 旗標與非 DSP 版預編譯庫 |
| `cmake\extension.cmake` | 建置完成訊息去掉換行。CMake 4 會把它寫進 `rules.ninja`，Ninja 隨即停止並回報 `lexing error` |
| `tools\serial_monitor.py` | 支援 Windows COM 埠，不需要 pyserial；指令參數不變 |
| `build_freertos.ps1` | 新增於 SDK 根目錄，用法見下一節 |

修正記錄寫在 SDK 根目錄的 `WINDOWS-HOST-FIXES.json`（每個檔案修改前後的 SHA-256），安裝記錄 `online-install.json` 的 `sdkWindowsHostFixes` 指向它。原始 SDK 壓縮檔保留在下載快取。

確認修正已生效：建置一次後，在建置記錄中找 `[toolchain]`：

```powershell
Select-String -Path apps\platform\ipro7ai_reef_demo\build\build.log -Pattern "\[toolchain\]"
```

```text
-- [toolchain] Vendor toolchain detected - enabling xxldsp and mtune=intelpro-ipro7
```

## PowerShell 命令列建置

`build_freertos.ps1` 的動作與選項與 `build_freertos.sh` 相同：`build`、`clean`、`flash`、`monitor`、`size`、`test` 可以組合，依序執行。它使用同一個安裝目錄中的工具鏈、CMake、Ninja、Python 與 IntelProCube，沒有勾選加入 PATH 也能執行。Windows 預設不允許直接執行 `.ps1`；以 `powershell -ExecutionPolicy Bypass -File` 執行只影響這一次，不會改變系統設定。

1. **切換到 SDK 目錄**

    ```powershell
    cd C:\IPRO\versions\3.6.0-<日期時間>\sdk\IPRO-SDK-V2.2.0
    ```

2. **建置**

    ```powershell
    powershell -ExecutionPolicy Bypass -File .\build_freertos.ps1 build ipro7ai_reef_demo
    ```

    ```text
    Project: ipro7ai_reef_demo  (platform/ipro7ai_reef_demo)

    -> Building ipro7ai_reef_demo...
    [OK] Build succeeded (21s)
      Binary: ...\apps\platform\ipro7ai_reef_demo\build\build_out\ipro7ai_reef_demo_IPRO7.bin (219 KB)
      Toolchain: C:/IPRO/versions/3.6.0-<日期時間>/toolchain/riscv_ipro7_win32/bin/riscv64-unknown-elf-gcc
      Log: ...\apps\platform\ipro7ai_reef_demo\build\build.log
    ```

    失敗時會印出建置記錄的最後 30 行與完整記錄的位置。

3. **燒錄並開啟主控台**：以 USB-C 接上 IPRO7AI Reef EVK 的 IPRO Debug Probe。腳本會自動選用 probe 的 UART 埠，由 IntelProCube 自動進入下載模式，燒錄後自動重置板子。

    ```powershell
    powershell -ExecutionPolicy Bypass -File .\build_freertos.ps1 flash monitor ipro7ai_reef_demo -w "IPRO7AI Reef carrier demo"
    ```

    ```text
    [All Success]
    [OK] Flash succeeded (6s)
    -> Opening serial monitor on COM23...
    ...
    I (0) [503329144] REEF:   IPRO7AI Reef carrier demo
    [serial_monitor] MATCHED: IPRO7AI Reef carrier demo

    Summary:
      [OK] flash
      [OK] monitor
    ```

    COM 埠號依電腦而定。接了多個序列埠或沒有使用 probe 時，以 `-p COM5` 指定主控台的埠；沒有 probe 時，請先依開發板說明按 BOOT＋RESET 進入下載模式。燒錄前請關閉其他開著該 COM 埠的終端程式。

4. **自動測試**（可選）：單元測試的設定是 EVB，在 Reef 上要指定晶片配置與分割表。

    ```powershell
    powershell -ExecutionPolicy Bypass -File .\build_freertos.ps1 test ipro_unit_test --chip-variant IPRO7AI --pt chips/ipro7/partition/partition_cfg_2M.toml -t 120
    ```

    最後應看到 `Result:         ALL TESTS PASSED` 與 `[OK] All tests passed`。

其他用法：`-l` 列出所有應用，`-h` 顯示所有選項，`-d defconfig_<名稱>` 以替代設定建置，`-c <前綴>` 指定其他工具鏈（前綴以 `riscv64-unknown-elf-` 結尾）。

## 後續發布規則

- **SDK：**在 `INTELPRO-INC/IPRO-SDK` 發布上述命名的正式 Release，附上根目錄同名的完整 SDK 壓縮檔。須有 `CMakeLists.txt`、`apps/`、`bsp/`、相符的 `VERSION.txt` 與 GitHub asset SHA-256。下一次執行同一支 EXE 就會找到新版。
- **工具：**透過既有套件管理中心發布對應的 ZIP 並設為穩定版。管理工具會更新公開清單中的下載網址、版本、大小、SHA-256、目錄與相依性，下一次執行同一支 EXE 即會使用更新。單純上傳未登錄的任意工具檔案不會被誤認為可安裝套件。
- 安裝器 EXE 發布於 `ipro-sdk-releases`；SDK 與工具資料來源仍是 `IPRO-SDK`。SDK、工具、Extension、安裝器的版本各自獨立。
- 若改變套件格式、安裝能力或需修正安裝器本身，才需要更新 EXE。

## 指定版本與離線使用

可用 `/SDKVERSION=2.2.0` 固定 SDK；其他元件分別使用 `/TOOLCHAINVERSION=`、`/TOOLSVERSION=`、`/FLASHVERSION=`、`/OPENOCDVERSION=`、`/EXTENSIONVERSION=`。不指定時使用 `latest`。

`/MODE=sdk`（或 `toolchain`）搭配 `/ENV=<既有環境資料夾>` 把 SDK 或工具鏈加到該環境；`/TOOLCHAINROOT=<工具鏈資料夾>` 指定 SDK-only 安裝要搭配的工具鏈（資料夾內應有 `bin\riscv64-unknown-elf-ld.exe`）。靜默安裝（`/VERYSILENT`）只在指定 `/ENV=` 時才會加到既有環境。

`/OFFLINE=1` 使用上次已驗證的清單與完整下載快取，沒有快取清單時使用內附快照（建置時的清單，SDK 2.2.0）；離線無法查詢新版。`/CATALOG=<本機 JSON>` 可指定快照。網路模式查詢失敗會停止，避免默默安裝舊版；GitHub API 流量限制時請稍後重試。

## Windows 工具鏈相容處理

工具鏈為 GCC 13.1.1（binutils 2.40）。SDK 2.2.0 有 5 個預編譯庫（`liblp_framework`、`liblc3`、`libai`、`libisp_algorithm`、`libipro7_rf`）包含 binutils 2.41 以前的 linker 不支援的 DWARF relocation。搭配的工具鏈是 binutils 2.41 以前的版本時，安裝器只移除這些預編譯庫的除錯資訊，保留程式碼，並寫入 `PREBUILT-ARCHIVES-STRIPPED.json` 記錄前後 SHA-256；原始下載檔保留於快取。搭配較新的工具鏈則不處理。若 relocation 位於非除錯區段，安裝會停止並要求新版 linker。移除除錯資訊後，無法對這些預編譯庫作原始碼層級除錯。

這項處理依「搭配的工具鏈」決定，與安裝模式無關；各模式搭配哪一套工具鏈見「單獨安裝 SDK 或工具鏈」一節。安裝記錄 `online-install.json` 的 `sdkToolchainPreparation` 說明實際做了什麼。

SDK 2.2.0 的 macOS／Linux 建置使用 GCC 14，Windows 以 GCC 13.1.1 建出的映像與其不同。

## 驗證與記錄

本版在 Windows 11 x64 驗證：完整安裝（SDK 2.2.0、toolchain 13.1.1-20230713、tools 2026.09.09、IntelProCube 0.7.0、OpenOCD 2024.06、Extension 3.3.0）與完成頁；SDK-only 裝到新資料夾（自動配對其他環境的工具鏈、以 `/TOOLCHAINROOT=` 指定）後，在 PATH 不含任何建置工具下建置與燒錄成功；SDK 2.1.0 加到既有環境後建置成功，環境原有檔案不變；同版本 SDK／工具鏈加到既有環境、Full 模式加到既有環境都被拒絕且環境不變；離線模式；安裝精靈各頁、既有環境清單與同版本提示；Windows 主機修正在已修正的 SDK 上不重複套用。安裝後以 VS Code Extension 的方式（CMake 3.30.5 + Ninja）建置，確認辨識 IPRO 工具鏈；在 PATH 不含任何建置工具的情況下，以 `build_freertos.ps1` 建置 `ipro7ai_reef_demo`、透過 IPRO Debug Probe 燒錄 IPRO7AI Reef EVK 並看到開機訊息，`ipro_unit_test` 155 項 0 失敗。

安裝記錄位於安裝目錄內的 `online-install.json`、`online-postinstall.log`，下載記錄位於相鄰的 `<安裝目錄>.download.log`。加到既有環境時，記錄寫在該環境的 `online-install.d\`，下載記錄為相鄰的 `<環境資料夾>.<日期時間>-<模式>.download.log`。本版未做 ARM64 實機測試，EXE 未作 Authenticode 簽章。
