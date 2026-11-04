# Package catalog

`index-v3.json` is the package catalog that IPRO SDK Online Setup 3.7.0 and later read on every
run, from `https://raw.githubusercontent.com/INTELPRO-INC/ipro-sdk-releases/main/catalog/index-v3.json`.
Keep the path and the file name: published installers depend on them.

Each package family downloads only from its own repository, and every package is checked against
the size and SHA-256 listed here:

| Family | Repository |
| --- | --- |
| sdk | [IPRO-SDK](https://github.com/INTELPRO-INC/IPRO-SDK) (2.x versions are found from its releases at run time) |
| toolchain | [ipro-toolchain](https://github.com/INTELPRO-INC/ipro-toolchain) |
| tools (CMake, Ninja, Python) | [ipro-build-tools](https://github.com/INTELPRO-INC/ipro-build-tools) |
| flash (IntelProCube) | [ipro-cube](https://github.com/INTELPRO-INC/ipro-cube) |
| openocd | [ipro-openocd](https://github.com/INTELPRO-INC/ipro-openocd) |
| extension | [ipro-vscode-extension](https://github.com/INTELPRO-INC/ipro-vscode-extension) |

Online Setup 3.4.0 to 3.6.0 read `packages/index.json` of IPRO-SDK instead; that file is unchanged.

## 繁體中文

`index-v3.json` 是 IPRO SDK Online Setup 3.7.0 以後每次執行時讀取的版本清單。已發布的安裝程式依賴這個路徑與檔名，請勿搬移或改名。每一類套件只接受來自上表所列倉庫的下載，並以這裡記錄的大小與 SHA-256 驗證。3.4.0–3.6.0 仍讀取 IPRO-SDK 的 `packages/index.json`。
