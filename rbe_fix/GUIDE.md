# RBE for LineageOS 18.1 (AOSP 11) - Guide

## Prerequisites
- Sudah `repo init` + `repo sync` LineageOS 18.1
- Punya akun BuildBuddy (https://buildbuddy.io)
- Di root android tree (dimana ada `build/make`, `build/soong`, `prebuilts/`, dll)

## Step-by-step

### 1. Copy folder rbe_fix ke root android tree
```bash
cp -r rbe_fix <root-android>/
```

### 2. Build patched reclient
Script otomatis pake Go dari `prebuilts/go/linux-x86/bin/go` (AOSP). Kalo gak ada, dia **auto-download** Go 1.21 sendiri.
```bash
cd <root-android>
bash rbe_fix/scripts/build_patched_reclient.sh .
```

### 3. Apply adapted patch untuk AOSP 11
patch -p1 < rbe_fix/patches/aosp11-rbe-buildbuddyfix-defaults.patch

### 4. Set environment variables BuildBuddy
export USE_RBE=1
export RBE_service="your-instance.buildbuddy.io:443"
export RBE_remote_headers="x-buildbuddy-api-key=xxx"
export RBE_use_rpc_credentials=false
export RBE_service_no_auth=true

export NINJA_REMOTE_NUM_JOBS=128
export RBE_use_unified_downloads=true
export RBE_use_unified_uploads=true

export RBE_CXX_EXEC_STRATEGY=remote_local_fallback
export RBE_JAVAC_EXEC_STRATEGY=remote_local_fallback
export RBE_R8_EXEC_STRATEGY=remote_local_fallback
export RBE_D8_EXEC_STRATEGY=remote_local_fallback

export RBE_CXX=1
export RBE_JAVAC=1
export RBE_R8=1
export RBE_D8=1

### 5. Build normal
source build/envsetup.sh
lunch <target>
make -j$(nproc)

## Opsional: Auto-setiap build
Tambahkan env vars di atas ke `build/envsetup.sh` atau `vendor/lineage/build/envsetup.sh`

## Files
rbe_fix/
├── GUIDE.md                          <- Guide ini
├── patches/
│   ├── aosp11-rbe-buildbuddyfix-defaults.patch  <- Patch untuk AOSP 11
│   └── reclient-buildbuddy-root-working-dir.patch <- Patch reclient source
└── scripts/
    └── build_patched_reclient.sh     <- Script build reclient from source
