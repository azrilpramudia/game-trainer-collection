# Technical Design Document (DESIGN)

# PVZ Trainer --- Plants vs. Zombies Original (2009)

**Versi dokumen:** 1.0\
**Status:** Draft --- rancangan teknis awal\
**Mengacu pada:** `PRD-PVZ-Trainer.md`\
**Platform:** Windows 10/11\
**Bahasa:** C++17 (baseline), C++20 dapat dipertimbangkan\
**UI:** Dear ImGui (pilihan awal)\
**Build system:** CMake

------------------------------------------------------------------------

## 1. Tujuan Dokumen

Dokumen ini menjelaskan bagaimana PVZ Trainer akan dirancang dan
dibangun berdasarkan kebutuhan pada PRD. Fokusnya adalah struktur
aplikasi, tanggung jawab modul, alur data, kontrak antarmodul, rancangan
UI, konfigurasi profil, logging, pengujian, dan strategi kompatibilitas.

Dokumen ini bukan panduan alamat memori siap pakai. Struktur internal
game, alamat, offset, dan mekanisme perubahan state harus diteliti serta
diverifikasi secara terpisah untuk executable yang benar-benar diuji.

## 2. Prinsip Desain

1.  **Modular:** UI, process management, memory access, cheat logic, dan
    konfigurasi dipisahkan.
2.  **Fail closed:** jika versi game tidak cocok atau operasi tidak
    tervalidasi, fitur tidak dijalankan.
3.  **Explicit connection:** aplikasi tidak mengubah proses sebelum
    pengguna menghubungkan trainer ke game.
4.  **Least privilege:** minta hak akses proses seminimal mungkin.
5.  **Reversible where possible:** setiap fitur memiliki mekanisme
    disable/restore yang jelas.
6.  **Version-aware:** data yang spesifik terhadap build game berada di
    profil kompatibilitas, bukan tersebar di kode.
7.  **Observable:** status fitur dan error dapat dilihat melalui UI dan
    log.
8.  **Testable:** logika umum dapat diuji tanpa harus menjalankan game.

## 3. Gambaran Arsitektur

Arsitektur menggunakan pemisahan berlapis:

``` text
┌──────────────────────────────────────────────┐
│                  Presentation                 │
│              Dear ImGui / UI                  │
└──────────────────────┬───────────────────────┘
                       │ User actions / ViewModel
┌──────────────────────▼───────────────────────┐
│                 Application                   │
│       AppController / TrainerController       │
└──────────────┬───────────────────┬───────────┘
               │                   │
┌──────────────▼─────────┐  ┌──────▼───────────┐
│     Cheat Services     │  │ Config & Logging│
│ Resources / Plants     │  │ Profiles / Logs │
│ Zombies / Game         │  └──────────────────┘
└──────────────┬─────────┘
               │ Validated interfaces
┌──────────────▼───────────────────────────────┐
│             Process & Memory Layer            │
│ ProcessManager / ProcessSession / MemoryReader │
│ MemoryWriter (restricted, validated)           │
└──────────────┬───────────────────────────────┘
               │ Windows API
┌──────────────▼───────────────────────────────┐
│             Windows Operating System          │
└──────────────────────────────────────────────┘
```

### 3.1 Alur Utama

1.  Aplikasi dimulai dan membaca konfigurasi.
2.  UI menampilkan status `Not Connected`.
3.  Pengguna menjalankan game dan memilih **Connect**.
4.  `ProcessManager` mencari executable yang sesuai.
5.  `CompatibilityService` memeriksa identitas build terhadap profil.
6.  Jika valid, `ProcessSession` menyimpan handle dan metadata proses.
7.  Cheat services dapat meminta operasi melalui interface memori
    tervalidasi.
8.  UI menampilkan status berdasarkan hasil operasi, bukan hanya input
    pengguna.
9.  Saat Disconnect atau proses game berhenti, layanan cheat
    dinonaktifkan dan handle dilepas.

## 4. Struktur Direktori

``` text
pvz-trainer/
├── CMakeLists.txt
├── CMakePresets.json
├── README.md
├── LICENSE
├── .gitignore
├── docs/
│   ├── PRD.md
│   ├── DESIGN.md
│   └── COMPATIBILITY.md
├── config/
│   └── profiles/
│       └── pvz-original-template.json
├── assets/
│   └── icons/
├── include/
│   └── pvz/
│       ├── app/
│       │   ├── AppController.hpp
│       │   └── TrainerState.hpp
│       ├── process/
│       │   ├── ProcessInfo.hpp
│       │   ├── ProcessManager.hpp
│       │   └── ProcessSession.hpp
│       ├── compatibility/
│       │   ├── GameBuild.hpp
│       │   └── CompatibilityService.hpp
│       ├── memory/
│       │   ├── IMemoryReader.hpp
│       │   ├── IMemoryWriter.hpp
│       │   └── MemoryResult.hpp
│       ├── cheats/
│       │   ├── ICheat.hpp
│       │   ├── CheatManager.hpp
│       │   ├── resources/
│       │   ├── plants/
│       │   ├── zombies/
│       │   └── game/
│       ├── config/
│       │   ├── AppConfig.hpp
│       │   └── ProfileRepository.hpp
│       ├── logging/
│       │   └── Logger.hpp
│       └── ui/
│           ├── MainWindow.hpp
│           ├── DashboardView.hpp
│           ├── ResourcesView.hpp
│           ├── PlantsView.hpp
│           ├── ZombiesView.hpp
│           ├── GameView.hpp
│           └── SettingsView.hpp
├── src/
│   ├── main.cpp
│   ├── app/
│   ├── process/
│   ├── compatibility/
│   ├── memory/
│   ├── cheats/
│   │   ├── resources/
│   │   ├── plants/
│   │   ├── zombies/
│   │   └── game/
│   ├── config/
│   ├── logging/
│   └── ui/
├── tests/
│   ├── unit/
│   │   ├── config/
│   │   ├── compatibility/
│   │   └── cheats/
│   └── integration/
└── third_party/
    └── imgui/
```

`third_party/` hanya untuk dependensi yang memang dikelola langsung di
repository. Alternatifnya, dependensi dapat diambil melalui package
manager atau CMake FetchContent sesuai kebijakan proyek.

## 5. Tanggung Jawab Modul

### 5.1 App Layer

**AppController** - Menginisialisasi konfigurasi, logger, process
manager, compatibility service, cheat manager, dan UI. - Mengelola
lifecycle aplikasi. - Menyediakan operasi Connect dan Disconnect. -
Meneruskan hasil operasi ke state UI.

**TrainerState** - Menyimpan status koneksi. - Menyimpan daftar status
fitur. - Menyediakan data yang dibutuhkan UI. - Tidak melakukan operasi
Windows API secara langsung.

Contoh status koneksi:

``` cpp
enum class ConnectionState {
    NotConnected,
    Searching,
    Connected,
    Incompatible,
    Error
};
```

### 5.2 Process Layer

**ProcessManager** - Melakukan enumerasi proses Windows. - Mencocokkan
nama executable berdasarkan profil. - Mengembalikan metadata proses,
misalnya PID dan nama executable. - Tidak melakukan operasi cheat.

**ProcessSession** - Membungkus handle proses yang telah dibuka. -
Memastikan handle dilepas melalui RAII. - Menyediakan pemeriksaan apakah
proses masih aktif. - Menjadi konteks sesi untuk operasi memori.

**ProcessInfo** - PID. - Nama executable. - Arsitektur proses jika dapat
diketahui. - Informasi versi/build yang dikumpulkan oleh compatibility
service.

Gunakan wrapper RAII agar `HANDLE` selalu ditutup. Hindari menyebarkan
raw handle ke UI.

### 5.3 Compatibility Layer

**CompatibilityService** - Membaca metadata executable yang
diperlukan. - Membandingkannya dengan profil yang dipilih. -
Menghasilkan status kompatibilitas. - Menentukan fitur mana yang
tersedia pada build tersebut.

Status kompatibilitas yang disarankan: - `Verified`: build cocok dengan
profil yang diuji. - `Unverified`: build belum diuji. - `Unsupported`:
build diketahui tidak didukung. - `Mismatch`: identitas build tidak
cocok.

Aplikasi harus menonaktifkan fitur yang memerlukan data build tertentu
jika statusnya bukan `Verified`.

### 5.4 Memory Layer

Memory layer menjadi satu-satunya bagian aplikasi yang berinteraksi
langsung dengan memori proses game.

Interface konseptual:

``` cpp
struct MemoryResult {
    bool success;
    std::string message;
};

class IMemoryReader {
public:
    virtual ~IMemoryReader() = default;
    // API pembacaan tervalidasi ditentukan pada implementasi.
};

class IMemoryWriter {
public:
    virtual ~IMemoryWriter() = default;
    // API penulisan dibatasi pada operasi yang telah diverifikasi.
};
```

Tanggung jawab: - Memastikan sesi proses masih valid. - Memeriksa hasil
operasi Windows API. - Memvalidasi ukuran dan tipe data. - Mengembalikan
hasil terstruktur. - Mencatat error yang relevan. - Tidak mengasumsikan
alamat memori berlaku untuk semua build.

**Penting:** jangan membuat satu fungsi penulisan memori generik yang
dapat dipanggil sembarang bagian aplikasi tanpa validasi. Semua operasi
penulisan harus melewati layanan fitur yang memiliki profil
kompatibilitas dan pemeriksaan yang sesuai.

### 5.5 Cheat Layer

Setiap fitur mengikuti kontrak yang seragam. Contoh konseptual:

``` cpp
enum class CheatStatus {
    Disabled,
    Enabling,
    Enabled,
    Disabling,
    Unsupported,
    Failed
};

class ICheat {
public:
    virtual ~ICheat() = default;

    virtual std::string id() const = 0;
    virtual std::string displayName() const = 0;
    virtual CheatStatus status() const = 0;

    virtual bool canEnable() const = 0;
    virtual void enable() = 0;
    virtual void disable() = 0;
};
```

Implementasi nyata dapat memakai return type yang lebih kuat seperti
`std::expected` jika standar dan compiler mendukungnya, atau tipe
`Result<T>` milik proyek.

**CheatManager** - Mendaftarkan fitur. - Memeriksa dukungan berdasarkan
profil. - Mengaktifkan dan menonaktifkan fitur. - Menghentikan fitur
saat sesi game berakhir. - Mencegah dua fitur yang saling bertentangan
aktif bersamaan.

### 5.6 Config Layer

**AppConfig** - Preferensi UI. - Lokasi file log. - Profil game yang
dipilih. - Pengaturan interval pembaruan status.

**ProfileRepository** - Membaca dan memvalidasi file profil. - Menolak
profil dengan format tidak valid. - Menyediakan metadata kompatibilitas
kepada compatibility service. - Tidak menganggap konfigurasi sebagai
data tepercaya tanpa validasi.

### 5.7 Logging Layer

Logger mencatat: - Waktu kejadian. - Level log (`DEBUG`, `INFO`, `WARN`,
`ERROR`). - Nama modul atau fitur. - Pesan status. - Kode error sistem
jika relevan.

Jangan mencatat data yang tidak diperlukan. Log harus membantu
troubleshooting tanpa membocorkan informasi pribadi.

## 6. Desain Fitur dan Pengelompokan

### 6.1 Resource Services

  Class konseptual        Fitur
  ----------------------- -----------------
  `UnlimitedSunCheat`     Unlimited Sun
  `SetSunService`         Set Sun
  `UnlimitedCoinsCheat`   Unlimited Coins
  `SetCoinsService`       Set Coins
  `AutoCollectCheat`      Auto Collect

`SetSunService` dan `SetCoinsService` lebih cocok sebagai aksi satu kali
daripada toggle terus-menerus. UI menggunakan tombol Apply dan validasi
angka.

### 6.2 Plant Services

  Class konseptual            Fitur
  --------------------------- ---------------------
  `NoPlantCooldownCheat`      No Plant Cooldown
  `FreePlantCheat`            Free Plant
  `PlantInvincibilityCheat`   Plant Invincibility
  `InstantRechargeCheat`      Instant Recharge
  `PlantAnywhereCheat`        Plant Anywhere

No Plant Cooldown dan Instant Recharge harus diuji untuk memastikan
keduanya memang memberi perilaku yang berbeda. Jika mekanismenya sama,
tampilkan satu fitur dengan deskripsi yang jelas agar tidak
membingungkan.

### 6.3 Zombie Services

  Class konseptual             Fitur
  ---------------------------- ----------------------
  `FreezeZombiesCheat`         Freeze Zombies
  `NoZombieAttackCheat`        No Zombie Attack
  `OneHitKillCheat`            One Hit Kill
  `KillAllZombiesAction`       Kill All Zombies
  `ZombieInvincibilityCheat`   Zombie Invincibility

`KillAllZombiesAction` merupakan aksi sekali jalan, bukan toggle. UI
menggunakan tombol dengan konfirmasi.

### 6.4 Game Services

  Class konseptual           Fitur
  -------------------------- ---------------------------
  `StopTimerCheat`           Stop Timer
  `GameSpeedService`         Fast Game dan Slow Motion
  `InstantWinAction`         Instant Win
  `UnlockPlantsService`      Unlock All Plants
  `UnlockMiniGamesService`   Unlock All Mini Games
  `UnlockSurvivalService`    Unlock Survival Mode

Fast Game dan Slow Motion dapat menjadi satu service dengan pilihan
multiplier, apabila mekanisme game yang diteliti mendukungnya.

Fitur unlock yang menyentuh save/profile harus memakai komponen
backup/restore terpisah. Jangan mencampurkan manipulasi file save dengan
memory session.

## 7. Alur Operasi

### 7.1 Startup

``` text
Start Application
      |
Load App Config
      |
Initialize Logger
      |
Initialize Services
      |
Initialize UI
      |
Show Not Connected
```

### 7.2 Connect ke Game

``` text
User clicks Connect
        |
ProcessManager searches process
        |
Process found?
   /          \
 No            Yes
 |              |
Show error    Read build metadata
                  |
           Profile compatible?
              /       \
            No         Yes
            |           |
       Block cheats   Create session
                         |
                   Update UI Connected
```

### 7.3 Mengaktifkan Fitur

``` text
User enables feature
        |
Check process session
        |
Check profile compatibility
        |
Check feature support
        |
Run feature validation
        |
Operation successful?
     /          \
   No            Yes
   |              |
Set Failed      Set Enabled
Log error       Update UI
```

### 7.4 Disconnect atau Game Exit

1.  Hentikan task/polling milik fitur.
2.  Jalankan prosedur disable/restore yang aman dan masih memungkinkan.
3.  Tandai fitur sebagai `Disabled` atau `FailedToRestore` jika
    pemulihan tidak dapat dipastikan.
4.  Lepaskan process handle.
5.  Kosongkan state sesi.
6.  Perbarui UI menjadi `Not Connected`.

Jangan mencoba menulis ke proses yang sudah berhenti. Jika proses sudah
keluar, catat bahwa pemulihan melalui proses tidak lagi tersedia.

## 8. Rancangan UI

### 8.1 Layout Utama

``` text
┌──────────────────────────────────────────────────────────┐
│ PVZ Trainer                                      v1.0.0  │
├──────────────────────────────────────────────────────────┤
│ Game: Plants vs. Zombies Original                        │
│ Profile: [PVZ Original profile       v]                   │
│ Status: NOT CONNECTED              [ Connect ]            │
├───────────────┬──────────────────────────────────────────┤
│ Dashboard     │ Overview                                 │
│ Resources     │                                          │
│ Plants        │  Game Process: Not detected              │
│ Zombies       │  Compatibility: Not checked              │
│ Game          │  Active Cheats: 0                         │
│ Settings      │                                          │
│ Logs          │  Connect to the game to enable features.  │
└───────────────┴──────────────────────────────────────────┘
```

### 8.2 Resources View

-   Toggle: Unlimited Sun.
-   Input + Apply: Set Sun.
-   Toggle: Unlimited Coins.
-   Input + Apply: Set Coins.
-   Toggle: Auto Collect.

### 8.3 Plants View

-   Toggle: No Plant Cooldown.
-   Toggle: Free Plant.
-   Toggle: Plant Invincibility.
-   Toggle: Instant Recharge.
-   Toggle: Plant Anywhere.

### 8.4 Zombies View

-   Toggle: Freeze Zombies.
-   Toggle: No Zombie Attack.
-   Toggle: One Hit Kill.
-   Button: Kill All Zombies.
-   Toggle: Zombie Invincibility.

### 8.5 Game View

-   Toggle: Stop Timer.
-   Control: Game Speed.
-   Button: Instant Win.
-   Button: Unlock All Plants.
-   Button: Unlock All Mini Games.
-   Button: Unlock Survival Mode.

Aksi yang mengubah save harus menampilkan dialog yang menyebutkan file
yang akan dicadangkan dan diubah.

### 8.6 Settings dan Logs

Settings: - Profil game. - Lokasi log. - Preferensi tema. - Interval
pembaruan status. - Informasi versi aplikasi.

Logs: - Daftar pesan log. - Filter level log. - Tombol Clear View (tidak
menghapus file log kecuali diminta). - Tombol Open Log Folder.

### 8.7 State UI

Setiap fitur minimal memiliki state: - Disabled. - Enabling. -
Enabled. - Disabling. - Unsupported. - Failed.

UI tidak boleh menyimpulkan `Enabled` hanya karena checkbox dicentang.
Checkbox harus mencerminkan state layanan yang telah dikonfirmasi.

## 9. Model Data Konfigurasi

Contoh bentuk profil template. Nilai identitas build harus diisi setelah
executable target diperiksa dan diuji.

``` json
{
  "profile_schema_version": 1,
  "profile_id": "pvz-original-template",
  "display_name": "Plants vs. Zombies Original (Template)",
  "executable_names": [
    "PlantsVsZombies.exe",
    "popcapgame1.exe"
  ],
  "build": {
    "version": "UNVERIFIED",
    "architecture": "unknown",
    "verification_status": "unverified"
  },
  "features": {
    "unlimited_sun": {
      "available": false,
      "status": "unverified"
    },
    "set_sun": {
      "available": false,
      "status": "unverified"
    }
  }
}
```

Template tersebut sengaja tidak menyertakan alamat atau offset. Nilai
kompatibilitas hanya boleh ditambahkan setelah diverifikasi untuk build
yang spesifik.

Validasi konfigurasi: - Versi schema harus dikenali. - `profile_id`
harus unik. - Nama executable harus berupa nama file, bukan path
arbitrer. - Fitur yang tidak dikenal diabaikan atau ditolak secara
aman. - Profil `unverified` tidak boleh mengaktifkan fitur yang
membutuhkan identitas build terverifikasi.

## 10. Error dan Result Handling

Hindari hanya mengembalikan `true/false` untuk seluruh operasi. Gunakan
tipe hasil yang menyertakan kode dan pesan.

Contoh konseptual:

``` cpp
enum class ErrorCode {
    None,
    ProcessNotFound,
    AccessDenied,
    ProcessExited,
    IncompatibleBuild,
    UnsupportedFeature,
    InvalidInput,
    OperationFailed,
    BackupFailed
};

struct OperationResult {
    bool success;
    ErrorCode code;
    std::string message;
};
```

Prinsip: - Error harus diteruskan ke UI. - Error sistem dapat dicatat
dengan kode Windows yang relevan. - Operasi yang gagal tidak boleh
mengubah state menjadi Enabled. - Error yang berulang tidak boleh
menghasilkan spam log tanpa batas.

## 11. Threading dan Lifecycle

Untuk versi pertama, utamakan desain sederhana: - UI berjalan pada main
thread. - Operasi Connect/Disconnect dilakukan sebagai task singkat. -
Polling status proses menggunakan interval terbatas. - Fitur yang
memerlukan pemantauan berulang harus memiliki lifecycle dan mekanisme
stop yang eksplisit. - Jangan membuat thread baru untuk setiap
checkbox. - Semua worker harus dihentikan sebelum service dan process
session dihancurkan.

Jika operasi asinkron diperlukan, gunakan `std::jthread` pada C++20 atau
pengelolaan thread yang eksplisit pada C++17.

## 12. Dependency dan Build

### 12.1 Dependency yang Dipertimbangkan

  Dependency            Kegunaan       Catatan
  --------------------- -------------- --------------------------------------------------
  Dear ImGui            UI             Backend Win32 + renderer yang dipilih
  nlohmann/json         JSON config    Alternatif: JSON library lain
  spdlog                Logging        Opsional; logger sederhana cukup untuk prototipe
  Catch2 / GoogleTest   Unit testing   Pilih salah satu
  CMake                 Build system   Direkomendasikan

Jaga dependency seminimal mungkin. Jangan menambahkan library jika
kebutuhan dapat dipenuhi dengan standard library atau Windows API secara
sederhana.

### 12.2 Konfigurasi Build

-   Gunakan CMake.
-   Tetapkan C++17 sebagai baseline.
-   Gunakan warning compiler yang sesuai.
-   Pisahkan konfigurasi Debug dan Release.
-   Jangan commit binary game atau file save pengguna ke repository.
-   Jangan commit file build sementara.

Contoh perintah build setelah CMake project disiapkan:

``` powershell
cmake -S . -B build
cmake --build build --config Debug
```

Perintah ini merupakan alur build umum; target dan dependency akan
disesuaikan ketika `CMakeLists.txt` dibuat.

## 13. Strategi Testing

### 13.1 Unit Tests

Uji tanpa game: - Parser konfigurasi. - Validasi profil. - State
transition fitur. - Validasi input Set Sun/Coins. - Error mapping. -
Backup manager menggunakan file uji.

### 13.2 Integration Tests

Uji pada lingkungan Windows lokal: - Enumerasi proses. - Koneksi dan
disconnect. - Proses berhenti saat trainer aktif. - Build game tidak
cocok. - Operasi ditolak oleh OS. - Logging dan UI state.

### 13.3 Feature Tests

Setiap fitur harus memiliki test record: - Game build yang diuji. -
Kondisi awal. - Langkah pengujian. - Hasil yang diharapkan. - Hasil
aktual. - Status: Pass / Fail / Unsupported. - Catatan pemulihan dan
efek samping.

Jalankan satu fitur pada satu waktu saat penelitian awal agar penyebab
crash atau perubahan state lebih mudah ditelusuri.

## 14. Compatibility Matrix

Buat `docs/COMPATIBILITY.md` dengan format seperti berikut:

  ----------------------------------------------------------------------------------------
  Game                 OS          Architecture   Feature       Status      Notes
  distribution/build                                                        
  -------------------- ----------- -------------- ------------- ----------- --------------
  Belum ditentukan     Windows     Belum          Process       Pending     Identifikasi
                       10/11       diperiksa      detection                 executable

  Belum ditentukan     Windows     Belum          Unlimited Sun Pending     Belum diuji
                       10/11       diperiksa                                

  Belum ditentukan     Windows     Belum          Plant cheats  Pending     Belum diuji
                       10/11       diperiksa                                

  Belum ditentukan     Windows     Belum          Zombie cheats Pending     Belum diuji
                       10/11       diperiksa                                

  Belum ditentukan     Windows     Belum          Game/unlock   Pending     Perlu evaluasi
                       10/11       diperiksa                                save
  ----------------------------------------------------------------------------------------

Jangan mengubah status menjadi `Verified` sebelum pengujian pada build
yang dicatat selesai.

## 15. Keamanan, Pemulihan, dan Batasan

-   Target hanya proses game lokal yang dipilih pengguna.
-   Tidak ada injeksi ke proses lain yang tidak terkait.
-   Tidak ada bypass DRM atau anti-cheat.
-   Jangan menonaktifkan proteksi Windows.
-   Gunakan akses proses minimum.
-   Semua operasi harus memeriksa status sesi.
-   Perubahan save wajib memiliki backup.
-   Fitur yang tidak dapat dipulihkan harus diberi peringatan sebelum
    dijalankan.
-   Pengguna harus dapat menutup trainer tanpa meninggalkan worker yang
    berjalan.

## 16. Keputusan Desain

  Area           Keputusan awal              Alasan
  -------------- --------------------------- -------------------------------------
  Bahasa         C++17                       Dukungan compiler luas
  UI             Dear ImGui                  Cepat untuk utility desktop
  Build          CMake                       Reproducible dan modular
  Arsitektur     Layered + service-based     Pemisahan tanggung jawab
  Config         JSON                        Mudah dibaca dan diedit
  Logging        spdlog atau logger ringan   Diagnostik
  Profil         Per-build profile           Menghindari asumsi alamat universal
  Fitur unlock   Backup/restore terpisah     Melindungi save pengguna

Keputusan dapat diperbarui jika prototipe menunjukkan pilihan yang lebih
sesuai.

## 17. Rencana Implementasi Teknis

Urutan implementasi yang disarankan:

1.  **Bootstrap:** CMake, `main.cpp`, logger, config loader.
2.  **Process detection:** enumerasi proses dan menampilkan PID.
3.  **Process session:** lifecycle handle dan pemeriksaan proses.
4.  **Compatibility:** metadata executable dan validasi profil.
5.  **UI shell:** dashboard dan status koneksi.
6.  **Memory abstraction:** interface reader/writer dengan pemeriksaan
    hasil.
7.  **Resource MVP:** riset dan implementasi Set Sun / Unlimited Sun
    untuk build terverifikasi.
8.  **Cheat manager:** registrasi, status, enable/disable, dan cleanup.
9.  **Feature expansion:** tanaman, zombie, game, lalu unlock.
10. **QA/release:** compatibility matrix, regression test, README, dan
    build release.

## 18. Definition of Technical Done

Rancangan teknis dianggap siap untuk implementasi apabila: - Tanggung
jawab setiap modul jelas. - Alur koneksi dan disconnect terdefinisi. -
Status fitur memiliki state transition yang jelas. - Profil
kompatibilitas memiliki schema dan validasi. - UI tidak mengakses
process/memory API secara langsung. - Error handling dan logging
dirancang. - Mekanisme backup/restore dirancang untuk perubahan save. -
MVP dapat dibangun dan diuji tanpa harus mengimplementasikan semua cheat
sekaligus.

------------------------------------------------------------------------

## Lampiran A --- Pemetaan PRD ke Modul

  PRD area                     Modul utama
  ---------------------------- ---------------------
  Process detection            `process/`
  Build compatibility          `compatibility/`
  Memory operations            `memory/`
  Resource cheats              `cheats/resources/`
  Plant cheats                 `cheats/plants/`
  Zombie cheats                `cheats/zombies/`
  Game cheats                  `cheats/game/`
  Settings and profiles        `config/`
  Logging                      `logging/`
  User interface               `ui/`
  Lifecycle and coordination   `app/`

## Lampiran B --- Catatan Sebelum Coding

Sebelum membuat fitur cheat, pastikan distribusi dan build Plants
vs. Zombies yang akan dijadikan target sudah diketahui. Nama executable
saja tidak cukup untuk membuktikan kompatibilitas. Mulai dengan process
detection, metadata build, dan UI status. Jangan memasukkan alamat
memori yang belum diverifikasi ke dalam profil atau source code.
