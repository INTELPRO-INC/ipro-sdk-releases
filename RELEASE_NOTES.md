# IPRO SDK Online Setup 3.4.0

Windows online installer and IPRO7 SDK Installation and Setup Guide, published together for customer download.

## Assets

- **IPRO-SDK-Online-Setup-3.4.0.zip** — extract and run `IPRO-SDK-Online-Setup-3.4.0.exe`.
- **IPRO7_ISG_V1.0_en.pdf** — English Installation and Setup Guide v1.0, dated 2026-09-09.
- **SHA256SUMS.txt** — checksums for both downloads and the extracted EXE.

For a first installation, select **Full environment**, use a new installation directory, and follow the ISG for optional settings, editor setup and a first example build. The installer downloads packages online; this ZIP is not an offline SDK bundle. Customer installation does not require GitHub authentication.

The installer supports full installation and six single-package modes. Package versions are resolved from the official catalog and are independent of installer version 3.4.0. See the ISG for pinned versions and explicit offline installation with a prepared cache.

## Verification

The ZIP has been checked for archive integrity. The extracted EXE is 2,112,492 bytes and its SHA-256 matches the value documented in the ISG:

`00a391b8b92a1bb7177607d6527c1875a77e91500fe2c816d8816e72381cdd18`

The EXE is not Authenticode signed. Refer to ISG sections 1.2 and 12 for verification guidance and tested scope. No new Windows installation or hardware acceptance testing was performed for this publication.

See the [README](https://github.com/INTELPRO-INC/ipro-sdk-releases#readme) for download instructions and requirements.
