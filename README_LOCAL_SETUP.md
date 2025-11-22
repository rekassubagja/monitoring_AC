# Flutter Android App - Local Setup & Run Guide

Panduan ini untuk menjalankan aplikasi Flutter di mesin lokal Anda dengan tampilan native Android (realtime dengan hot reload).

## Prerequisites

- **Flutter SDK**: [Install Flutter](https://docs.flutter.dev/get-started/install)
- **Android SDK**: [Install Android Studio](https://developer.android.com/studio) atau setup command-line tools
- **Java 17+**: `java -version` harus menampilkan Java 17 atau lebih baru
- **HP Android** atau **Android Emulator**: dengan USB debugging aktif (untuk HP) atau emulator sudah berjalan

## Setup Lokal (Sekali Saja)

### 1. Clone Repository
```bash
git clone https://github.com/rekassubagja/monitoring_AC.git
cd monitoring_AC
```

### 2. Install Flutter (jika belum)
```bash
# Download Flutter SDK
git clone https://github.com/flutter/flutter.git -b stable

# Tambah Flutter ke PATH (sesuaikan dengan OS):
# Linux/macOS:
export PATH="$PWD/flutter/bin:$PATH"

# Windows: tambahkan folder flutter\bin ke environment variable PATH

# Verifikasi
flutter --version
```

### 3. Setup Android (jika belum)
```bash
# Jalankan flutter doctor untuk melihat status
flutter doctor -v

# Jika ada error, ikuti instruksi di output flutter doctor
# Paling penting:
# - Android SDK sudah terinstall
# - Android platform-tools di PATH
# - build-tools sudah terinstall
# - Setidaknya 1 Android platform (mis. android-33) terinstall
```

### 4. Hubungkan HP atau Jalankan Emulator

**Opsi A: Sambungkan HP via USB**
```bash
# Aktifkan USB Debugging di HP (Settings > Developer Options > USB Debugging)
# Sambungkan via USB kabel
# Izinkan akses saat diminta di HP

# Verifikasi HP terdeteksi
adb devices
# Harus muncul serial device dengan status 'device'
```

**Opsi B: Jalankan Emulator Android**
```bash
# Buat AVD (Android Virtual Device) jika belum ada
sdkmanager "system-images;android-33;google_apis;x86_64"
echo no | avdmanager create avd -n test_avd -k "system-images;android-33;google_apis;x86_64" --device "pixel"

# Jalankan emulator
emulator -avd test_avd &
```

## Menjalankan Aplikasi

### 1. Masuk Folder Project
```bash
cd monitoring_AC/my_app
```

### 2. Install Dependencies
```bash
flutter pub get
```

### 3. Lihat Devices yang Tersedia
```bash
flutter devices
```
Harus muncul HP atau emulator Anda di daftar.

### 4. Jalankan Aplikasi
```bash
# Jalankan ke device pertama yang terdeteksi
flutter run

# Atau tentukan device spesifik
flutter run -d <device-id>
```

Aplikasi akan dimulai di HP/emulator dan Anda akan melihat "Hello Pagi" di tengah layar.

## Hot Reload & Hot Restart

Saat aplikasi berjalan:
- **Hot Reload** (update UI cepat): tekan `r` di terminal atau klik tombol Hot Reload di IDE
- **Hot Restart** (restart app, reset state): tekan `R` di terminal
- **Quit**: tekan `q` di terminal

Edit file `lib/main.dart`, simpan, lalu tekan `r` — Anda akan melihat perubahan dalam hitungan detik di HP.

## Contoh Edit & Hot Reload

1. Edit `lib/main.dart`:
   ```dart
   child: Text('Hello Siang', style: TextStyle(fontSize: 24)),
   ```

2. Simpan file

3. Tekan `r` di terminal → teks langsung berubah menjadi "Hello Siang" di HP

## Build APK untuk Distribusi

Jika ingin generate APK untuk install di HP lain atau submit ke Play Store:
```bash
# Debug APK (untuk testing)
flutter build apk --debug
# Hasil: build/app/outputs/flutter-apk/app-debug.apk

# Release APK (untuk production - perlu signing)
flutter build apk --release
# Hasil: build/app/outputs/flutter-apk/app-release.apk
```

## Troubleshooting

### Masalah: "flutter: command not found"
- Pastikan Flutter sudah di-add ke PATH: `echo $PATH | grep flutter`
- Atau gunakan path lengkap: `/path/to/flutter/bin/flutter doctor`

### Masalah: "Unable to locate Android SDK"
- Jalankan: `flutter config --android-sdk /path/to/android-sdk`
- Atau atur environment: `export ANDROID_SDK_ROOT=/path/to/android-sdk`

### Masalah: HP tidak terdeteksi
```bash
# Cek USB debugging aktif di HP
adb devices
# Jika ada 'offline', cabut kabel USB, plug ulang, izinkan akses

# Atau restart adb daemon:
adb kill-server
adb devices
```

### Masalah: Emulator lambat
- Gunakan emulator x86_64 dengan KVM (Linux) untuk performa lebih baik
- Atau gunakan HP fisik (lebih cepat)

## File Struktur Project

```
monitoring_AC/
├── my_app/                          # Aplikasi Flutter utama
│   ├── lib/
│   │   └── main.dart               # Kode aplikasi (tempat untuk edit)
│   ├── android/                    # Native Android files (untuk konfigurasi)
│   ├── ios/                        # Native iOS files (untuk build iOS)
│   ├── pubspec.yaml                # Dependency management
│   └── build/                      # Build output (generated)
├── .devcontainer/                  # Konfigurasi Codespaces
├── .github/workflows/
│   └── flutter-android.yml         # CI/CD workflow (auto-build APK)
└── README.md                       # Dokumentasi repo
```

## Next Steps

- Edit `lib/main.dart` untuk customize UI
- Tambah dependencies di `pubspec.yaml` untuk fitur tambahan
- Build APK release untuk distribution
- Deploy ke Google Play Store (perlu konfigurasi signing)

## Links Berguna

- [Flutter Documentation](https://docs.flutter.dev)
- [Android Studio Setup](https://developer.android.com/studio)
- [Flutter Hot Reload](https://docs.flutter.dev/development/hot-reload)
- [Build & Release Android App](https://docs.flutter.dev/deployment/android)

---

**Setup & run via Codespaces/Actions**

Jika Anda ingin build APK di Codespaces:
1. Push perubahan ke `main`
2. Buka GitHub Actions
3. Workflow "Build Flutter Android APK" berjalan otomatis
4. Download artifact `app-release-apk`
5. Install ke HP: `adb install -r app-release.apk`
