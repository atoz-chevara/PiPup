# Homelab patches (branch `homelab-patches`)

Branch ini berisi perubahan minimal agar PiPup dapat dibangun dan dipakai di
homelab (Android TV: Realme/Changhong dan Xiaomi MiTV). Tidak ada perubahan
pada kode Kotlin — perilaku aplikasi identik dengan upstream `master` 172f2e6.

## Perubahan

### 1. `build.gradle` — `jcenter()` → `mavenCentral()`

Dua blok repositories (`buildscript.repositories` dan `allprojects.repositories`).
JCenter sudah tidak tersedia sehingga dependency lama (`com.android.support:*:28.0.0`,
glide 3.8.0, jackson 2.9.x, nanohttpd) gagal di-resolve tanpa perubahan ini.
`google()` tetap dipertahankan.

### 2. `app/src/main/AndroidManifest.xml` — `android:usesCleartextTraffic="true"`

Dibutuhkan karena `targetSdk 28` memblokir HTTP biasa. PiPup memuat gambar
notifikasi lewat `media.image.uri` menggunakan Glide; tanpa izin ini logo
tidak tampil dan tidak ada pesan error yang jelas.

Alternatif yang lebih ketat (belum diterapkan): `networkSecurityConfig` yang
hanya mengizinkan domain/IP server aset homelab.

### 3. `gradlew` — mode `100644` → `100755`

Agar `./gradlew assembleDebug` dapat dijalankan langsung setelah clone.
Tidak ada perubahan isi berkas.

## Build (terbukti)

```bash
export JAVA_HOME=/path/to/jdk11        # Gradle 5.1.1 tidak mendukung JDK 17+
export ANDROID_HOME=/path/to/android-sdk
export PATH=$JAVA_HOME/bin:$PATH
./gradlew assembleDebug --no-daemon
```

- JDK: Amazon Corretto 11.0.33
- Gradle wrapper: 5.1.1, AGP 3.4.1, Kotlin 1.3.31
- SDK: platforms;android-28, build-tools;28.0.3
- Hasil: `app/build/outputs/apk/debug/app-debug.apk` (±5,15 MB, debug-signed)

## Catatan lisensi

Upstream `rogro82/PiPup` tidak menyertakan berkas LICENSE. Fork ini dibuat
melalui mekanisme GitHub dan diturunkan apa adanya; tidak ada klaim lisensi
baru. Jangan mendistribusikan ulang sebagai produk tanpa izin pemilik aslinya.
