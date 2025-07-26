# LM Studio - Shared Models

# LM Studio Shared LLM Storage Setup (ZimaOS + Windows)

Centralized model storage for GGUF/LLMs across LAN-connected devices using LM Studio.

## ✨ Overview

This guide shows how to configure LM Studio to use a shared network drive (backed by ZimaOS on a K100 Flash NAS) for storing and resolving downloaded LLM models. This allows multiple Windows machines to use the same models without duplicating downloads.

---

## 📅 System Configuration Summary

| Component         | Details                                           |
| ----------------- | ------------------------------------------------- |
| **LLM Frontend**  | LM Studio on Windows 11 Home                      |
| **NAS OS**        | ZimaOS on K100 Flash NAS                          |
| **Storage Setup** | 3x 256GB SSDs in RAID0 (high-speed shared volume) |
| **Networking**    | 2.5GbE throughout                                 |
| **Windows Drive** | Shared drive mounted as `Z:`                      |

> ‼️ Future-ready: This setup can be tested with other LLM frontends later, but we're focusing on LM Studio compatibility for now.

---

## 🔄 Goal

Create a centralized, fast-access model repository on your NAS for use by multiple Windows devices running LM Studio. Models will be downloaded once and stored on a shared ZimaOS volume.

---

## ⚖️ Step-by-Step Setup Guide

### 1. 🗂️ Map NAS Shared Drive in Windows

* Open File Explorer
* Map `Z:` to your ZimaOS NAS volume (e.g. `\\ZIMA-NAS\llmshare`)
* Enable **"Reconnect at login"**

### 2. ❌ Close LM Studio

Ensure LM Studio is not running before proceeding.

### 3. 📁 Backup Existing Local Model Folder

```cmd
rename "%USERPROFILE%\.lmstudio\hub\models" models_backup
```

### 4. 🔌 Remove the Default Local Models Folder

```cmd
rmdir "%USERPROFILE%\.lmstudio\hub\models" /s /q
```

### 5. ⚙️ Symlink LM Studio Model Path to Shared Drive

```cmd
mklink /D "%USERPROFILE%\.lmstudio\hub\models" "Z:\"
```

Expected output:

```
symbolic link created for ... -> Z:\
```

### 6. 🚀 Test It!

* Launch LM Studio
* Download a model (e.g. from TheBloke)
* Confirm it appears in:

  ```
  Z:\TheBloke\Mistral-7B-Instruct-GGUF\
  ```

---

## 🔧 Tips for Multi-Device Setup

* All machines must:

  * Use the same mapped drive letter (e.g. `Z:`)
  * Use the same symlink method to redirect LM Studio model path
* Permissions: Ensure shared drive allows **read/write access** for all users
* Performance: RAID0 over SSDs and 2.5GbE networking ensures minimal bottlenecks

---

## 🧰 Future Enhancements

* Test shared model repo with other LLM UIs (e.g. LMDeploy, llama.cpp GUIs)
* Add model management scripts (e.g. sync, backup, prune)
* Explore version-locking or hashed storage for CI-like control

---

## ✅ Confirmed Working

* [x] LM Studio resolves models directly from `Z:`
* [x] Downloaded models persist and are shared between devices
* [x] No re-download required after machine switch



