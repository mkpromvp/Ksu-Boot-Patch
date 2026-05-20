# 🔧 KSU Boot Patcher — No Magisk / SUSFS Ready

GitHub Actions يعمل patch على boot.img بـ KernelSU بدون أي اعتماد على Magisk.

---

## ✅ SUSFS Support

| Variant | SUSFS في الكيرنال | ksud Linux |
|---------|-------------------|-----------|
| **SukiSU-Ultra** | ✅ Core Feature | ✅ |
| **KernelSU-Next** | ✅ مدعوم | ✅ |
| KernelSU (official) | ❌ مش في prebuilts | أحياناً |

> **الأفضل لـ SUSFS: SukiSU-Ultra أو KernelSU-Next**

---

## 🚀 الاستخدام

```
Actions → KSU Boot Patcher → Run workflow
```

| Input | وصف |
|-------|-----|
| `boot_img_url` | رابط مباشر لـ boot.img |
| `ksu_variant` | SukiSU-Ultra / KernelSU-Next / KernelSU |
| `ksu_version` | فاضي = أحدث إصدار |
| `multi_manager` | ZygiskNext + Shamiko |
| `include_susfs_module` | susfs4ksu userspace module |
| `include_lsposed` | LSPosed |

---

## 📦 Artifacts

- `ksu_patched_boot.img` + `KSUManager.apk`
- `modules/` — susfs4ksu, ZygiskNext, Shamiko, LSPosed

---

## 📱 Flash Order

```bash
adb reboot bootloader
fastboot flash boot ksu_patched_boot.img
fastboot reboot

# بعد الـ reboot:
# Install KSUManager.apk
# KSU Manager → Flash modules بالترتيب:
#   1. ZygiskNext.zip
#   2. Shamiko.zip
#   3. susfs4ksu.zip
# Reboot
```

---

## 🛡 SUSFS — إيه ده؟

SUSFS (SUS File System) هو patch على مستوى الكيرنال بيخفي الروت من أي تطبيق حتى لو استخدم أقوى طرق الكشف. بيكمّل ZygiskNext + Shamiko.

- **Kernel side**: موجود في prebuilt الـ GKI من SukiSU-Ultra / KernelSU-Next
- **Userspace side**: module `susfs4ksu` بتعمله flash من KSU Manager
