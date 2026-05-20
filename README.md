# 🔧 KSU Boot Patcher — Multi Manager + SUSFS

A GitHub Actions workflow that patches a stock `boot.img` with a pre-built GKI kernel that includes **KernelSU**, **SUSFS**, and **Multi Manager** support — no kernel source required.

---

## ✨ What It Does

1. Downloads your stock `boot.img` from a direct link
2. Detects the kernel version and KMI automatically
3. Downloads a matching pre-built GKI kernel with SUSFS baked in
4. Replaces the kernel inside your `boot.img`
5. Uploads the patched `boot.img` + manager APKs + modules as artifacts

---

## 🔀 What Is "Multi Manager"?

One patched kernel that works with **multiple manager APKs**. Flash once, then switch between managers freely without reflashing:

| GKI Source | Compatible Managers |
|------------|-------------------|
| **WildKernels** | Wild KSU Manager ✅ + KernelSU-Next ✅ |
| **zzh20188** | SukiSU ✅ + MKSU ✅ + KSU-Next ✅ + ReSukiSU ✅ |
| **KernelSU-Next** | KernelSU-Next ✅ |

---

## 🛡 What Is SUSFS?

SUSFS (SUS File System) is a **kernel-level** root hiding patch. It hides root traces at the lowest level possible, making detection extremely difficult even for apps that inspect `/proc`, file paths, and mount points.

- **Kernel side** — baked into the GKI prebuilt (no extra step needed)
- **Userspace side** — the `ksu_module_susfs` module (flash via KSU Manager)

---

## 🚀 How to Use

### Step 1 — Create the repository

Create a new GitHub repository and place the workflow file at:
```
.github/workflows/ksu-patcher.yml
```

### Step 2 — Get a direct link to your stock boot.img

Upload your `boot.img` to any file host that gives a **direct download link**:
- Google Drive (use a direct-link converter)
- Telegram (copy file link)
- Any file hosting with direct URLs

### Step 3 — Run the workflow

```
GitHub → Your repo → Actions → KSU Boot Patcher → Run workflow
```

Fill in the inputs:

| Input | Description |
|-------|-------------|
| `boot_img_url` | Direct download link to your stock `boot.img` |
| `gki_source` | Choose your preferred kernel source |
| `include_susfs_module` | Downloads the SUSFS userspace module |
| `include_zygisk` | Downloads ZygiskNext + Shamiko |
| `include_lsposed` | Downloads LSPosed |

### Step 4 — Download the artifacts

After the workflow finishes (usually ~1–2 min), download:

- **KSU-Boot** artifact → contains `ksu_patched_boot.img` + manager APKs
- **KSU-Modules** artifact → contains all selected modules

---

## 📱 Flashing Instructions

### Flash the patched boot

```bash
# Reboot to fastboot mode
adb reboot bootloader

# Flash
fastboot flash boot ksu_patched_boot.img

# Reboot
fastboot reboot
```

### Install a Manager APK

Install any compatible manager from the artifact (or both):
```bash
adb install WildKSU_Manager.apk
# or
adb install KernelSU-Next_Manager.apk
```

### Flash Modules (in KSU Manager)

Open KSU Manager → **Modules** → flash in this order:
1. `ksu_module_susfs.zip`
2. `ZygiskNext.zip` *(if selected)*
3. `Shamiko.zip` *(if selected)*
4. `LSPosed.zip` *(if selected)*
5. **Reboot**

---

## ♻️ Restore Stock Boot

Flashing boot.img **cannot hard brick** your device. Worst case is a bootloop,
which is fully recoverable:

```bash
adb reboot bootloader
fastboot flash boot stock_boot.img
fastboot reboot
```

> Keep your original `stock_boot.img` saved somewhere safe before flashing.

---

## ⚠️ Requirements

- **GKI kernel** (Android 12+ / Kernel 5.10 or higher)
- **Unlocked bootloader**
- Stock `boot.img` from the **exact firmware** currently running on your device

---

## 📦 Sources & Credits

| Component | Source |
|-----------|--------|
| WildKernels GKI | [WildKernels/GKI_KernelSU_SUSFS](https://github.com/WildKernels/GKI_KernelSU_SUSFS) |
| zzh20188 GKI | [zzh20188/GKI_KernelSU_SUSFS](https://github.com/zzh20188/GKI_KernelSU_SUSFS) |
| KernelSU-Next | [KernelSU-Next/KernelSU-Next](https://github.com/KernelSU-Next/KernelSU-Next) |
| Wild KSU Manager | [WildKernels/Wild_KSU](https://github.com/WildKernels/Wild_KSU) |
| SukiSU Ultra | [SukiSU-Ultra/SukiSU-Ultra](https://github.com/SukiSU-Ultra/SukiSU-Ultra) |
| SUSFS Module | [sidex15/ksu_module_susfs](https://github.com/sidex15/ksu_module_susfs) |
| ZygiskNext | [Dr-TSNG/ZygiskNext](https://github.com/Dr-TSNG/ZygiskNext) |
| magiskboot | [topjohnwu/Magisk](https://github.com/topjohnwu/Magisk) *(used as unpack tool only — not installed as root)* |

---

## ❓ FAQ

**Will this hard brick my device?**
No. Flashing `boot.img` only touches the boot partition. Worst case is a bootloop, fixed by reflashing the stock boot.

**Do I need to reflash if I want to switch managers?**
No. Just uninstall the current manager APK and install another compatible one.

**Why is SUSFS better than just Shamiko?**
Shamiko hides root at the userspace level. SUSFS hides it at the kernel level — much deeper and harder to detect.

**Does this work on all Android devices?**
Only on devices with a **GKI kernel** (Android 12 and above on most Qualcomm/MediaTek flagships). Non-GKI devices need a custom kernel build from source.

**Artifacts expire — what do I do?**
Re-run the workflow. It will fetch the latest available versions automatically.
