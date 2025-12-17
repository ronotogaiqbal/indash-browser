# Indash Browser - Claude Code Guidance

## Project Overview

Indash Mobile adalah aplikasi Android native yang berfungsi sebagai WebView wrapper untuk menampilkan aplikasi web INDASH dalam mode fullscreen. Aplikasi ini memuat URL `http://103.210.69.56:8100` dan menyediakan fitur geolocation serta immersive mode.

## Tech Stack

- **Language**: Kotlin 1.9.21
- **Min SDK**: 24 (Android 7.0)
- **Target SDK**: 34 (Android 14)
- **Build System**: Gradle 8.2
- **Android Gradle Plugin**: 8.2.0
- **JVM Target**: Java 17

## Project Structure

```
app/
├── src/main/
│   ├── java/com/example/indashmobile/
│   │   └── MainActivity.kt          # Logic utama aplikasi
│   ├── res/
│   │   ├── drawable/                # Vector icons
│   │   ├── layout/
│   │   │   └── activity_main.xml    # Layout WebView
│   │   ├── mipmap-anydpi-v26/       # Adaptive launcher icons
│   │   └── values/
│   │       ├── colors.xml
│   │       ├── strings.xml
│   │       └── themes.xml
│   └── AndroidManifest.xml
├── build.gradle                      # App build config
└── proguard-rules.pro
```

## Build Commands

```bash
./gradlew assembleDebug      # Build debug APK
./gradlew assembleRelease    # Build release APK
./gradlew clean              # Clean build
./gradlew build              # Build semua variant
```

Output APK: `app/build/outputs/apk/debug/app-debug.apk`

## Key Configuration

### Target URL
```kotlin
// MainActivity.kt
private val TARGET_URL = "http://103.210.69.56:8100"
```

### Permissions (AndroidManifest.xml)
- `INTERNET` - Akses jaringan
- `ACCESS_NETWORK_STATE` - Cek status jaringan
- `ACCESS_FINE_LOCATION` - Geolokasi presisi
- `ACCESS_COARSE_LOCATION` - Geolokasi kasar

### WebView Settings
```kotlin
javaScriptEnabled = true
domStorageEnabled = true
databaseEnabled = true
setGeolocationEnabled(true)
mixedContentMode = MIXED_CONTENT_ALWAYS_ALLOW
useWideViewPort = true
loadWithOverviewMode = true
allowFileAccess = true
```

## Features

1. **Fullscreen/Immersive Mode**
   - Menyembunyikan status bar dan navigation bar
   - Swipe untuk menampilkan sementara
   - Kompatibel Android 11+ dan versi lama

2. **Geolocation**
   - Runtime permission request
   - Callback ke WebView untuk izin lokasi

3. **Back Navigation**
   - Kembali dalam history WebView jika ada
   - Keluar aplikasi jika tidak ada history

4. **Configuration Changes**
   - Handle rotasi layar tanpa restart activity
   - Maintain state saat keyboard muncul

## Code Conventions

### Single Activity Pattern
Aplikasi menggunakan satu Activity (`MainActivity`) tanpa Fragment.

### Permission Handling
```kotlin
private val locationPermissionLauncher = registerForActivityResult(
    ActivityResultContracts.RequestMultiplePermissions()
) { permissions ->
    // Handle permission result
}
```

### Fullscreen Implementation
- Android 11+: Gunakan `WindowInsetsController`
- Legacy: Gunakan `systemUiVisibility` flags

## CI/CD

GitHub Actions workflow di `.github/workflows/build.yml`:
- Trigger: push ke main/master, pull request
- Build debug APK otomatis
- Upload artifact dengan retention 30 hari

## Important Files

| File | Deskripsi |
|------|-----------|
| `MainActivity.kt` | Logic utama, WebView setup, permissions |
| `AndroidManifest.xml` | Permissions, activity config |
| `activity_main.xml` | Layout dengan FrameLayout + WebView |
| `themes.xml` | Material DayNight theme, fullscreen |
| `build.gradle` (app) | Dependencies, SDK versions |

## Theming

- Primary: `#1976D2` (Material Blue)
- Primary Dark: `#1565C0`
- Status Bar: Black
- Navigation Bar: Black
- No Action Bar (fullscreen)

## Security Notes

- `usesCleartextTraffic="true"` - HTTP traffic diizinkan
- File access enabled di WebView
- ProGuard disabled di release build
