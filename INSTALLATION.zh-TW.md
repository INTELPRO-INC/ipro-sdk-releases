# 動態線上安裝器 3.5.0

同一支 `IPRO-SDK-Online-Setup-3.5.0.exe` 每次執行安裝時都會重新查詢版本。SDK 或工具內容更新後，客戶不必重新下載或重新產生安裝程式。

## 下載與安裝

正式下載位置：<https://github.com/INTELPRO-INC/ipro-sdk-releases/releases/tag/v3.5.0>。

下載 EXE，或下載 ZIP 後解壓縮；以 `SHA256SUMS.txt` 核對檔案。首次安裝選 **Full environment**，保留各元件的 `latest`，使用尚不存在的新目錄。需要切換 VS Code 設定時，勾選設定切換工作，完成後重新開啟 VS Code。

安裝器也支援 SDK、toolchain、build tools、IntelProCube、OpenOCD、Extension 六種單獨安裝模式。既有目錄會保留；此功能是執行安裝時抓取更新，不是在背景修改目前使用中的 SDK。

## 版本從哪裡取得

| 元件 | 每次執行時查詢的來源 |
| --- | --- |
| SDK | `INTELPRO-INC/IPRO-SDK` 正式 Release 與 `packages/index.json` |
| Toolchain、CMake/Ninja/Python、IntelProCube、OpenOCD、Extension | 同一 SDK 倉庫 `main/packages/index.json` 中各元件的 stable 指標 |

SDK 自動辨識非草稿、非預覽版的 `IPRO-SDK-V<主版.次版.修訂版>`，按版本數值比較，不受整個 GitHub 倉庫的 Latest 標記或發布順序影響。辨識 `IPRO-SDK-V<版本>.tar.gz` 或 `.zip`，以 GitHub asset 的 SHA-256 與大小驗證下載，並核對解壓後的 `VERSION.txt`。版本選單直接顯示目前 `latest` 對應的版本。

清單中已登錄的同版 SDK 優先保留其相依性資訊；完整模式檢查已宣告的工具版本要求。直接從 Release 發現、尚未登錄相依性的 SDK 使用目前工具 stable 版本，不代表已驗證所有未來 SDK／工具組合。

## 後續發布規則

- **SDK：**在 `INTELPRO-INC/IPRO-SDK` 發布上述命名的正式 Release，附上根目錄同名的完整 SDK 壓縮檔。須有 `CMakeLists.txt`、`apps/`、`bsp/`、相符的 `VERSION.txt` 與 GitHub asset SHA-256。下一次執行同一支 EXE 就會找到新版。
- **工具：**透過既有套件管理中心發布對應的 ZIP 並設為穩定版。管理工具會更新公開清單中的下載網址、版本、大小、SHA-256、目錄與相依性，下一次執行同一支 EXE 即會使用更新。單純上傳未登錄的任意工具檔案不會被誤認為可安裝套件。
- 安裝器 EXE 發布於 `ipro-sdk-releases`；SDK 與工具資料來源仍是 `IPRO-SDK`。SDK、工具、Extension、安裝器的版本各自獨立。
- 若改變套件格式、安裝能力或需修正安裝器本身，才需要更新 EXE。

## 指定版本與離線使用

可用 `/SDKVERSION=2.0.1` 固定 SDK；其他元件分別使用 `/TOOLCHAINVERSION=`、`/TOOLSVERSION=`、`/FLASHVERSION=`、`/OPENOCDVERSION=`、`/EXTENSIONVERSION=`。不指定時使用 `latest`。

`/OFFLINE=1` 使用上次已驗證的清單與完整下載快取，沒有快取清單時使用內附快照；離線無法查詢新版。`/CATALOG=<本機 JSON>` 可指定快照。網路模式查詢失敗會停止，避免默默安裝舊版；GitHub API 流量限制時請稍後重試。

## Windows 工具鏈相容處理

SDK 2.0.1 的部分預編譯庫包含舊版 Windows linker 不支援的 DWARF relocation。Full 模式若使用 binutils 2.41 以前版本，會延用既有安裝流程的處理：只移除受影響預編譯庫的除錯資訊，保留程式碼，並寫入 `PREBUILT-ARCHIVES-STRIPPED.json` 記錄前後 SHA-256。原始下載檔保留於快取。新版 linker 不需此處理；若 relocation 位於非除錯區段，安裝會停止並要求新版 linker。

SDK-only 模式保留原始 SDK；搭配舊工具鏈時可能仍遇到上述連結問題，建議使用 Full 模式建立完整環境。移除除錯資訊後無法對這些預編譯庫作原始碼層級除錯。

## 驗證與記錄

本版驗證涵蓋 Windows x64、匿名下載 SDK 2.0.1、完整安裝、指定 SDK 1.2.1、同一 EXE 遇到模擬新版 SDK／工具，以及安裝後 `ipro7ai_evk_demo` 編譯。模擬版本只存在於本機測試，未發布到正式清單。另驗證 SHA-256／網址拒絕、解壓路徑穿越、連結、重複名稱與大小限制。

安裝記錄位於安裝目錄內的 `online-install.json`、`online-postinstall.log`，下載記錄位於相鄰的 `<安裝目錄>.download.log`。本版未做 ARM64 實機或板上燒錄測試，EXE 未作 Authenticode 簽章。
