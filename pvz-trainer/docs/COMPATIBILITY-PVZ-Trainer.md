# Compatibility Document

# PVZ Trainer --- Plants vs. Zombies Original (2009)

**Versi dokumen:** 1.0\
**Status:** Draft --- baseline pengujian belum ditetapkan\
**Dokumen terkait:** `PRD.md`, `DESIGN.md`\
**Platform trainer:** Windows 10/11\
**Target game:** Plants vs. Zombies Original (2009)

------------------------------------------------------------------------

## 1. Tujuan

Dokumen ini mencatat kompatibilitas PVZ Trainer terhadap distribusi dan
build Plants vs. Zombies yang diuji. Dokumen menjadi sumber acuan untuk
mengetahui executable yang didukung, fitur yang telah diverifikasi,
keterbatasan, dan hasil pengujian.

Nama executable saja tidak cukup untuk membuktikan kompatibilitas. Dua
distribusi dapat menggunakan nama proses serupa tetapi memiliki versi,
struktur internal, atau perilaku yang berbeda.

Semua status dalam dokumen ini harus didasarkan pada pengujian aktual.
Jangan menandai versi atau fitur sebagai `Verified` hanya berdasarkan
perkiraan atau tutorial pihak lain.

## 2. Ruang Lingkup Kompatibilitas

Pengujian kompatibilitas meliputi:

-   Distribusi/edisi game.
-   Versi dan build executable.
-   Nama executable.
-   Arsitektur proses.
-   Versi Windows.
-   Deteksi dan koneksi proses.
-   Fitur trainer per kategori.
-   Perilaku ketika game ditutup atau trainer disconnect.
-   Dampak fitur terhadap kestabilan game dan save/profile.

Target awal adalah satu build Plants vs. Zombies Original (2009) yang
tersedia pada lingkungan pengembangan. Kompatibilitas edisi GOTY,
non-GOTY, Steam, atau distribusi lainnya tidak diasumsikan sama.

## 3. Definisi Status

  -----------------------------------------------------------------------
  Status                              Makna
  ----------------------------------- -----------------------------------
  `Verified`                          Sudah diuji pada kombinasi
                                      versi/build dan lingkungan yang
                                      dicatat; hasil sesuai harapan.

  `Partial`                           Berfungsi, tetapi memiliki
                                      keterbatasan atau kondisi tertentu.

  `Failed`                            Sudah diuji, tetapi tidak berfungsi
                                      sesuai harapan.

  `Pending`                           Belum diuji.

  `Unsupported`                       Secara sengaja tidak didukung atau
                                      diketahui tidak kompatibel.

  `Unverified`                        Informasi versi/build belum cukup
                                      untuk menyatakan kompatibilitas.
  -----------------------------------------------------------------------

Status kompatibilitas build dan status fitur harus dicatat secara
terpisah. Build yang teridentifikasi belum tentu berarti seluruh fitur
pada build tersebut sudah teruji.

## 4. Game Distribution Matrix

Matriks berikut adalah daftar target pemeriksaan, bukan pernyataan bahwa
semua edisi telah didukung.

  --------------------------------------------------------------------------
  Distribusi /   Versi / Build  Executable     Arsitektur     Status
  Edisi                                                       
  -------------- -------------- -------------- -------------- --------------
  Original 2009  Belum          Belum          Belum          Pending
  non-GOTY       diperiksa      diperiksa      diperiksa      

  Original 2009  Belum          Belum          Belum          Pending
  GOTY           diperiksa      diperiksa      diperiksa      

  Steam Edition  Belum          Belum          Belum          Pending
                 diperiksa      diperiksa      diperiksa      

  Distribusi     Belum          Belum          Belum          Unverified
  lainnya        diperiksa      diperiksa      diperiksa      
  --------------------------------------------------------------------------

### 4.1 Informasi yang Harus Dicatat

Untuk setiap instalasi/build yang diuji, catat:

-   Nama distribusi atau edisi.
-   Sumber instalasi yang sah.
-   Nama executable aktual.
-   File version/product version jika tersedia.
-   Ukuran file.
-   Arsitektur executable.
-   Hash file (misalnya SHA-256) untuk membedakan build secara
    konsisten.
-   Versi Windows dan arsitektur OS.
-   Tanggal pengujian.
-   Versi PVZ Trainer yang digunakan.

Hash hanya digunakan sebagai identitas file untuk pencatatan. Jangan
membagikan executable game atau file berhak cipta dalam repository.

## 5. Environment Compatibility Matrix

  ------------------------------------------------------------------------
  Komponen                Target awal              Status
  ----------------------- ------------------------ -----------------------
  Windows 10 64-bit       Lingkungan               Pending
                          pengembangan/pengujian   

  Windows 11 64-bit       Pengujian tambahan       Pending

  Windows 32-bit          Di luar target awal      Unsupported / belum
                                                   dievaluasi

  Trainer 32-bit          Ditentukan saat build    Pending

  Trainer 64-bit          Ditentukan saat build    Pending

  Game 32-bit             Harus diidentifikasi     Pending
                          dari executable          

  Game 64-bit             Harus diidentifikasi     Pending
                          dari executable          
  ------------------------------------------------------------------------

Catatan: - Jangan menyimpulkan arsitektur game dari arsitektur Windows
saja. - Arsitektur trainer dan game perlu diperiksa secara eksplisit. -
Kemampuan membaca proses 32-bit dari aplikasi 64-bit atau sebaliknya
memiliki batasan teknis tertentu. Pilih konfigurasi yang telah diuji dan
dokumentasikan hasilnya.

## 6. Feature Compatibility Matrix

Semua fitur dimulai dengan status `Pending`. Ubah status hanya setelah
pengujian dilakukan pada build yang tercatat.

### 6.1 Process & Core

  -----------------------------------------------------------------------
  ID                Fitur             Status            Catatan
  ----------------- ----------------- ----------------- -----------------
  CORE-01           Process Detection Pending           Nama executable
                                                        harus
                                                        diverifikasi

  CORE-02           Connect /         Pending           Uji lifecycle
                    Disconnect                          handle

  CORE-03           Process Exit      Pending           Uji saat game
                    Detection                           ditutup

  CORE-04           Build             Pending           Identifikasi
                    Identification                      metadata
                                                        executable

  CORE-05           Profile           Pending           Uji profil cocok
                    Validation                          dan tidak cocok
  -----------------------------------------------------------------------

### 6.2 Resource Cheats

  -----------------------------------------------------------------------
  ID                Fitur             Status            Catatan
  ----------------- ----------------- ----------------- -----------------
  RES-01            Unlimited Sun     Pending           Verifikasi pada
                                                        build target

  RES-02            Set Sun           Pending           Validasi input
                                                        dan hasil

  RES-03            Unlimited Coins   Pending           Perlu
                                                        identifikasi
                                                        mekanisme Coins

  RES-04            Set Coins         Pending           Perlu validasi
                                                        perubahan nilai

  RES-05            Auto Collect      Pending           Perlu evaluasi
                                                        perilaku objek
  -----------------------------------------------------------------------

### 6.3 Plant Cheats

  -----------------------------------------------------------------------
  ID                Fitur             Status            Catatan
  ----------------- ----------------- ----------------- -----------------
  PLT-01            No Plant Cooldown Pending           Periksa perilaku
                                                        cooldown

  PLT-02            Free Plant        Pending           Uji konsumsi Sun

  PLT-03            Plant             Pending           Uji jenis damage
                    Invincibility                       yang berbeda

  PLT-04            Instant Recharge  Pending           Bandingkan dengan
                                                        No Plant Cooldown

  PLT-05            Plant Anywhere    Pending           Uji batas
                                                        penempatan dan
                                                        kestabilan
  -----------------------------------------------------------------------

### 6.4 Zombie Cheats

  ID       Fitur                  Status    Catatan
  -------- ---------------------- --------- ------------------------------
  ZMB-01   Freeze Zombies         Pending   Uji pergerakan dan animasi
  ZMB-02   No Zombie Attack       Pending   Uji interaksi dengan tanaman
  ZMB-03   One Hit Kill           Pending   Uji beberapa jenis zombie
  ZMB-04   Kill All Zombies       Pending   Uji berbagai jumlah zombie
  ZMB-05   Zombie Invincibility   Pending   Uji interaksi damage

### 6.5 Game Cheats

  -----------------------------------------------------------------------
  ID                Fitur             Status            Catatan
  ----------------- ----------------- ----------------- -----------------
  GAM-01            Stop Timer        Pending           Tentukan timer
                                                        yang dimaksud

  GAM-02            Fast Game         Pending           Uji kestabilan
                                                        simulasi

  GAM-03            Slow Motion       Pending           Uji kestabilan
                                                        simulasi

  GAM-04            Instant Win       Pending           Evaluasi kondisi
                                                        penyelesaian
                                                        level

  GAM-05            Unlock All Plants Pending           Perlu evaluasi
                                                        save/profile

  GAM-06            Unlock All Mini   Pending           Perlu evaluasi
                    Games                               save/profile

  GAM-07            Unlock Survival   Pending           Perlu evaluasi
                    Mode                                save/profile
  -----------------------------------------------------------------------

## 7. Profil Build

Setiap build yang didukung harus memiliki satu catatan profil. Contoh
template:

``` yaml
profile_id: pvz-original-build-template
display_name: Plants vs. Zombies Original - Unverified
distribution: unknown

executable:
  filename: unknown
  file_version: unknown
  product_version: unknown
  architecture: unknown
  sha256: unknown

environment:
  os: Windows 10/11
  os_architecture: unknown
  trainer_architecture: unknown

compatibility:
  status: unverified
  verified_date: null
  trainer_version: null

features:
  process_detection: pending
  unlimited_sun: pending
  set_sun: pending
  unlimited_coins: pending
  set_coins: pending
  auto_collect: pending
  no_plant_cooldown: pending
  free_plant: pending
  plant_invincibility: pending
  instant_recharge: pending
  plant_anywhere: pending
  freeze_zombies: pending
  no_zombie_attack: pending
  one_hit_kill: pending
  kill_all_zombies: pending
  zombie_invincibility: pending
  stop_timer: pending
  fast_game: pending
  slow_motion: pending
  instant_win: pending
  unlock_all_plants: pending
  unlock_all_mini_games: pending
  unlock_survival_mode: pending
```

Template di atas adalah format pencatatan. Ia belum menjadi profil aktif
dan tidak memuat alamat, offset, atau instruksi perubahan memori.

## 8. Prosedur Verifikasi Versi Game

Untuk setiap instalasi yang akan diuji:

1.  Jalankan game secara normal dan catat nama executable yang
    benar-benar berjalan.
2.  Periksa properti file executable untuk memperoleh informasi versi
    jika tersedia.
3.  Identifikasi arsitektur executable dengan tool pemeriksa PE yang
    sesuai.
4.  Hitung SHA-256 executable untuk membedakan build.
5.  Catat versi Windows dan arsitektur sistem.
6.  Buat profil baru dengan status `Unverified`.
7.  Uji process detection dan koneksi tanpa mengaktifkan fitur cheat.
8.  Pastikan Disconnect dan penutupan game ditangani dengan benar.
9.  Uji fitur satu per satu pada salinan lingkungan pengujian.
10. Perbarui status dan catatan berdasarkan hasil aktual.

Jangan menggunakan alamat atau offset dari build lain tanpa penelitian
dan verifikasi ulang. Jangan mengaktifkan fitur jika profil tidak cocok.

## 9. Prosedur Pengujian Fitur

Gunakan test record berikut untuk setiap fitur:

  Field                Isi
  -------------------- ------------------------
  Test ID              ID pengujian unik
  Game distribution    Edisi game
  Game build           Versi/build yang diuji
  Executable SHA-256   Hash executable
  Windows version      Versi Windows
  Trainer version      Versi trainer
  Feature              Nama fitur
  Preconditions        Kondisi awal
  Test steps           Langkah pengujian
  Expected result      Hasil yang diharapkan
  Actual result        Hasil aktual
  Status               Pass / Fail / Partial
  Recovery             Hasil disable/restore
  Notes                Catatan tambahan

### 9.1 Kriteria Lulus

Sebuah fitur dapat diberi status `Verified` apabila: - Diuji pada build
yang identitasnya tercatat. - Hasil sesuai perilaku yang ditentukan
PRD. - Tidak menyebabkan crash atau korupsi state selama pengujian. -
Disable/restore bekerja sebagaimana dirancang, atau keterbatasannya
dijelaskan. - Hasil dapat direproduksi pada pengujian ulang.

Untuk fitur yang mengubah save/profile, backup dan restore wajib diuji
sebelum status `Verified`.

## 10. Known Issues

Catat masalah yang ditemukan dalam format:

  ----------------------------------------------------------------------------------
  ID         Build        Fitur        Masalah    Dampak     Workaround   Status
  ---------- ------------ ------------ ---------- ---------- ------------ ----------
  KI-001     Belum        Belum        Belum ada  \-         \-           Open
             ditentukan   ditentukan   temuan                             

  ----------------------------------------------------------------------------------

Jangan mengisi known issue dengan dugaan. Masukkan hanya masalah yang
benar-benar ditemukan.

## 11. Aturan Feature Gating

Aplikasi harus menerapkan aturan berikut:

-   Jika game belum terdeteksi, semua fitur nonaktif.
-   Jika proses sudah berhenti, semua operasi dihentikan.
-   Jika build tidak cocok, fitur yang bergantung pada profil diblokir.
-   Jika profil belum diverifikasi, fitur berstatus `Unverified` dan
    tidak dapat diaktifkan secara default.
-   Jika satu fitur tidak didukung, fitur lain yang telah diverifikasi
    tetap dapat digunakan.
-   Status UI harus mengikuti hasil operasi aktual.
-   Jangan menganggap seluruh fitur kompatibel hanya karena satu fitur
    berhasil.

## 12. Changelog Kompatibilitas

  -----------------------------------------------------------------------
  Tanggal           Trainer Version   Game Build        Perubahan
  ----------------- ----------------- ----------------- -----------------
  Belum ada         \-                \-                Dokumen awal
                                                        dibuat; belum ada
                                                        build yang
                                                        diverifikasi

  -----------------------------------------------------------------------

Setiap perubahan status fitur, penambahan build, atau perubahan dukungan
harus dicatat di sini.

## 13. Checklist Sebelum Menyatakan Build Didukung

-   [ ] Nama distribusi/edisi dicatat.
-   [ ] Nama executable diverifikasi.
-   [ ] Versi/build executable dicatat.
-   [ ] Hash SHA-256 dicatat.
-   [ ] Arsitektur executable diperiksa.
-   [ ] Versi dan arsitektur Windows dicatat.
-   [ ] Process detection diuji.
-   [ ] Connect/disconnect diuji.
-   [ ] Process exit handling diuji.
-   [ ] Profil kompatibilitas dibuat.
-   [ ] Fitur diuji satu per satu.
-   [ ] Backup/restore diuji jika save diubah.
-   [ ] Known issues diperbarui.
-   [ ] Hasil pengujian dapat direproduksi.

------------------------------------------------------------------------

## Lampiran A --- Ringkasan Status Awal

Pada saat dokumen ini dibuat: - Belum ada distribusi atau build game
yang ditetapkan sebagai build resmi target. - Belum ada fitur trainer
yang berstatus `Verified`. - Seluruh fitur berada pada status
`Pending`. - Profil contoh hanya merupakan template dan belum boleh
digunakan untuk mengaktifkan fitur.

## Lampiran B --- Hubungan dengan Dokumen Lain

  -----------------------------------------------------------------------
  Dokumen                             Peran
  ----------------------------------- -----------------------------------
  `PRD.md`                            Menetapkan kebutuhan produk dan
                                      daftar fitur

  `DESIGN.md`                         Menetapkan arsitektur dan rancangan
                                      teknis

  `COMPATIBILITY.md`                  Mencatat build game, lingkungan,
                                      dan hasil verifikasi fitur

  `README.md`                         Menjelaskan project, cara build,
                                      dan cara penggunaan
  -----------------------------------------------------------------------

Dokumen ini harus diperbarui seiring pengujian dan pengembangan. Jangan
mengubah status kompatibilitas tanpa bukti pengujian yang dapat
ditelusuri.
