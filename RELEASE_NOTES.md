# IPRO SDK Online Setup 3.5.0

同一支安裝程式持續取得 SDK 與工具更新，不必因套件內容改版而重新產生 EXE。

- 每次線上安裝重新讀取工具套件清單，並自動辨識正式 SDK Release；目前可直接取得 SDK 2.0.1。
- 工具依各自的 stable 指標更新；維護者透過套件管理中心發布新版並設為穩定版後，客戶再次執行同一支 EXE 即可取得。
- 支援 SDK `.tar.gz` 與 ZIP，核對大小、SHA-256、目錄結構及 SDK 版本；保留指定舊版、下載快取與離線模式。
- 完整安裝接入既有 Windows 舊 linker 相容處理，並記錄受影響預編譯庫；SDK-only 保留原始 SDK。
- Windows x64 實測 SDK 2.0.1 下載、完整安裝及範例編譯；以本機模擬新版 SDK／工具驗證同一 EXE 無需重建。

下載 EXE 或 ZIP 後依 `SHA256SUMS.txt` 核對。首次安裝選 Full environment 與新目錄；需要切換 VS Code 時勾選設定切換工作。舊環境保留，更新不會在背景自動覆蓋既有目錄。

詳見 `INSTALLATION.zh-TW.md`。安裝器未作 Authenticode 簽章；本版沒有新增 ARM64 實機或板上燒錄驗證。

SDK 與工具仍取自 `INTELPRO-INC/IPRO-SDK`。此 Release 僅提供安裝包、校驗資訊與文件，不另行發布原始碼包。
