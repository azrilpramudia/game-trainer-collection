# Product Requirements Document (PRD)

# PVZ Trainer --- Plants vs. Zombies Original (2009)

**Versi dokumen:** 1.0\
**Status:** Draft --- spesifikasi awal\
**Platform target:** Windows 10/11\
**Bahasa implementasi:** C++\
**Target game:** Plants vs. Zombies Original (2009)\
**Jenis aplikasi:** Desktop game trainer untuk penggunaan lokal dan
single-player

------------------------------------------------------------------------

## 1. Ringkasan Produk

PVZ Trainer adalah aplikasi desktop Windows yang menyediakan kontrol
opsional terhadap sejumlah nilai dan perilaku permainan *Plants
vs. Zombies Original (2009)*. Aplikasi ditujukan sebagai proyek
pembelajaran C++ yang mempraktikkan process management, Windows API,
debugging, memory inspection, reverse engineering dasar, dan
pengembangan antarmuka desktop.

Trainer akan menyediakan fitur pengaturan resource, tanaman, zombie, dan
alur permainan. Fitur diaktifkan secara manual oleh pengguna dan
idealnya dapat dinonaktifkan kembali tanpa harus menutup aplikasi.

Aplikasi ini ditujukan untuk game lokal single-player. Proyek tidak
mencakup bypass DRM, pembajakan, cheat pada game online/multiplayer,
atau gangguan terhadap proses dan perangkat lunak pihak lain.

## 2. Latar Belakang dan Tujuan

### 2.1 Latar Belakang

Plants vs. Zombies merupakan game single-player yang memiliki sejumlah
nilai permainan seperti Sun, Coins, cooldown tanaman, status tanaman,
dan status zombie. Nilai atau perilaku tersebut dapat dipelajari melalui
proses observasi dan debugging pada executable lokal.

Proyek ini menjadi sarana belajar membangun aplikasi C++ yang
berinteraksi dengan proses Windows secara terkontrol.

### 2.2 Tujuan

1.  Membuat trainer desktop yang dapat mendeteksi proses game.
2.  Menyediakan antarmuka untuk mengaktifkan dan menonaktifkan fitur
    trainer.
3.  Mengimplementasikan seluruh fitur yang tercantum dalam dokumen ini
    sejauh dapat diverifikasi pada versi game target.
4.  Menerapkan pemisahan modul agar kode mudah dipelihara dan diuji.
5.  Menangani kegagalan akses proses, perubahan versi game, dan alamat
    memori yang tidak valid dengan aman.
6.  Menyediakan dokumentasi instalasi, penggunaan, batasan, dan
    troubleshooting.

### 2.3 Bukan Tujuan (Out of Scope)

-   Cheat untuk game online atau multiplayer.
-   Bypass DRM, lisensi, anti-cheat, atau sistem keamanan pihak ketiga.
-   Distribusi executable game, aset game, atau file berhak cipta.
-   Menjamin kompatibilitas dengan semua edisi, patch, atau executable
    modifikasi.
-   Mengubah file game secara permanen tanpa persetujuan pengguna.
-   Menjamin setiap fitur dapat diterapkan hanya melalui memory editing;
    sebagian fitur mungkin memerlukan pendekatan berbeda atau tidak
    layak diimplementasikan.

## 3. Target Pengguna

Pengguna utama adalah developer/pelajar yang: - Memahami dasar
pemrograman C++. - Ingin mempelajari Windows API dan pengelolaan
proses. - Ingin mempelajari debugging dan analisis memori pada aplikasi
lokal. - Memiliki salinan Plants vs. Zombies Original yang dapat
dijalankan di Windows.

## 4. Ruang Lingkup Fitur

Status berikut merupakan target produk, bukan klaim bahwa setiap fitur
telah terbukti bekerja pada semua versi game.

### 4.1 Resource Cheats

  -----------------------------------------------------------------------
  ID                Fitur             Perilaku yang     Prioritas
                                      diharapkan        
  ----------------- ----------------- ----------------- -----------------
  RES-01            Unlimited Sun     Sun tidak         Must
                                      berkurang ketika  
                                      tanaman dibeli    
                                      atau ditanam.     

  RES-02            Set Sun           Pengguna dapat    Must
                                      memasukkan jumlah 
                                      Sun tertentu.     

  RES-03            Unlimited Coins   Coins tidak       Should
                                      berkurang ketika  
                                      transaksi dalam   
                                      game dilakukan.   

  RES-04            Set Coins         Pengguna dapat    Should
                                      mengatur jumlah   
                                      Coins tertentu.   

  RES-05            Auto Collect      Membantu          Could
                                      mengumpulkan      
                                      objek Sun/Coins   
                                      yang muncul, jika 
                                      mekanisme game    
                                      memungkinkan.     
  -----------------------------------------------------------------------

### 4.2 Plant Cheats

  -----------------------------------------------------------------------
  ID                Fitur             Perilaku yang     Prioritas
                                      diharapkan        
  ----------------- ----------------- ----------------- -----------------
  PLT-01            No Plant Cooldown Menghilangkan     Must
                                      atau              
                                      mempersingkat     
                                      waktu tunggu      
                                      pemakaian         
                                      tanaman.          

  PLT-02            Free Plant        Tanaman dapat     Must
                                      digunakan tanpa   
                                      mengurangi Sun.   

  PLT-03            Plant             Tanaman tidak     Could
                    Invincibility     menerima atau     
                                      tidak             
                                      mempertahankan    
                                      efek kerusakan.   

  PLT-04            Instant Recharge  Mengatur cooldown Should
                                      agar tanaman siap 
                                      digunakan kembali 
                                      dengan cepat.     

  PLT-05            Plant Anywhere    Mengizinkan       Could
                                      penempatan        
                                      tanaman pada      
                                      lokasi yang       
                                      biasanya          
                                      dibatasi, jika    
                                      memungkinkan.     
  -----------------------------------------------------------------------

Catatan: PLT-01 dan PLT-04 berpotensi memiliki perilaku yang tumpang
tindih. Implementasi harus menjelaskan perbedaan keduanya atau
menggabungkannya jika game menggunakan mekanisme cooldown yang sama.

### 4.3 Zombie Cheats

  -----------------------------------------------------------------------
  ID                Fitur             Perilaku yang     Prioritas
                                      diharapkan        
  ----------------- ----------------- ----------------- -----------------
  ZMB-01            Freeze Zombies    Menghentikan atau Must
                                      menahan           
                                      pergerakan        
                                      zombie.           

  ZMB-02            No Zombie Attack  Mencegah zombie   Should
                                      menyerang tanaman 
                                      atau pemain.      

  ZMB-03            One Hit Kill      Membuat zombie    Should
                                      kalah setelah     
                                      menerima satu     
                                      serangan yang     
                                      valid.            

  ZMB-04            Kill All Zombies  Menghilangkan     Should
                                      seluruh zombie    
                                      aktif pada level  
                                      saat ini.         

  ZMB-05            Zombie            Membuat zombie    Could
                    Invincibility     tidak menerima    
                                      damage atau tidak 
                                      dapat dikalahkan. 
  -----------------------------------------------------------------------

### 4.4 Game Cheats

  -----------------------------------------------------------------------
  ID                Fitur             Perilaku yang     Prioritas
                                      diharapkan        
  ----------------- ----------------- ----------------- -----------------
  GAM-01            Stop Timer        Menghentikan      Could
                                      timer atau        
                                      komponen waktu    
                                      tertentu pada     
                                      mode yang         
                                      mendukungnya.     

  GAM-02            Fast Game         Mempercepat       Could
                                      simulasi          
                                      permainan.        

  GAM-03            Slow Motion       Memperlambat      Could
                                      simulasi          
                                      permainan.        

  GAM-04            Instant Win       Menyelesaikan     Could
                                      level secara      
                                      instan jika       
                                      kondisi           
                                      kemenangan dapat  
                                      diubah dengan     
                                      aman.             

  GAM-05            Unlock All Plants Membuka akses     Could
                                      tanaman yang      
                                      terkunci.         

  GAM-06            Unlock All Mini   Membuka minigame  Could
                    Games             yang terkunci.    

  GAM-07            Unlock Survival   Membuka akses     Could
                    Mode              Survival Mode.    
  -----------------------------------------------------------------------

Catatan penting: - Fitur unlock dapat berhubungan dengan save/profile
game, bukan hanya memori proses. Implementasi harus mengidentifikasi
mekanisme penyimpanan yang benar sebelum mengubah data. - Perubahan save
harus memiliki fitur backup dan pemulihan. - Instant Win, Plant
Anywhere, Stop Timer, dan pengubahan kecepatan game berpotensi
bergantung pada struktur internal game dan dapat dinyatakan tidak
didukung jika tidak stabil.

## 5. Kebutuhan Fungsional

### 5.1 Deteksi dan Koneksi Game

-   **FR-01:** Aplikasi dapat mencari proses game berdasarkan nama
    executable yang telah dikonfigurasi.
-   **FR-02:** Aplikasi menampilkan status `Game Not Detected`,
    `Game Detected`, atau `Connection Error`.
-   **FR-03:** Aplikasi dapat menghubungkan diri ke proses game setelah
    pengguna menekan tombol Connect.
-   **FR-04:** Aplikasi memeriksa apakah proses masih berjalan sebelum
    melakukan operasi.
-   **FR-05:** Aplikasi tidak boleh menganggap koneksi berhasil jika
    akses proses ditolak atau proses telah berhenti.
-   **FR-06:** Aplikasi menampilkan informasi PID dan arsitektur proses
    jika tersedia.

Nama executable dapat berbeda antar distribusi. Nama proses harus
dikonfigurasi melalui profil, bukan ditanam sebagai satu-satunya asumsi.

### 5.2 Pengelolaan Cheat

-   **FR-07:** Setiap fitur memiliki kontrol enable/disable.
-   **FR-08:** Fitur yang memerlukan input angka menyediakan validasi
    rentang.
-   **FR-09:** Aplikasi menampilkan status aktif, nonaktif, tidak
    didukung, atau gagal.
-   **FR-10:** Aplikasi tidak menjalankan fitur sebelum proses game
    terdeteksi dan koneksi valid.
-   **FR-11:** Ketika game ditutup, seluruh operasi berulang dihentikan
    dan status koneksi diperbarui.
-   **FR-12:** Jika suatu fitur gagal, aplikasi menampilkan pesan yang
    dapat dipahami dan tidak terus memaksakan operasi.

### 5.3 Pengaturan dan Profil

-   **FR-13:** Pengguna dapat memilih profil versi game.
-   **FR-14:** Konfigurasi trainer disimpan pada file konfigurasi
    terpisah.
-   **FR-15:** Aplikasi tidak menyimpan alamat memori yang belum
    diverifikasi sebagai alamat universal.
-   **FR-16:** Profil harus memiliki identitas versi/build dan
    pemeriksaan kecocokan sebelum fitur dijalankan.

### 5.4 Logging

-   **FR-17:** Aplikasi mencatat waktu, fitur, status operasi, dan pesan
    error.
-   **FR-18:** Log tidak boleh menyimpan informasi sensitif yang tidak
    dibutuhkan.
-   **FR-19:** Pengguna dapat membuka folder log dari aplikasi.

### 5.5 Backup dan Pemulihan Save

-   **FR-20:** Sebelum fitur unlock mengubah file save, aplikasi harus
    membuat salinan cadangan.
-   **FR-21:** Aplikasi memberi tahu pengguna file apa yang akan diubah.
-   **FR-22:** Pengguna dapat membatalkan perubahan.
-   **FR-23:** Aplikasi menyediakan cara mengembalikan backup.

## 6. Kebutuhan Non-Fungsional

### 6.1 Stabilitas

-   Aplikasi tidak boleh crash hanya karena game belum berjalan.
-   Operasi memori harus memvalidasi handle, ukuran data, dan hasil
    operasi.
-   Fitur yang tidak kompatibel harus dinonaktifkan, bukan dipaksakan.

### 6.2 Keamanan dan Batasan

-   Hanya bekerja pada proses game lokal yang dipilih pengguna.
-   Meminta hak akses minimum yang diperlukan.
-   Tidak menjalankan kode tersembunyi atau mengunduh payload.
-   Tidak menonaktifkan antivirus atau proteksi sistem.
-   Tidak mengubah file game/save tanpa persetujuan dan backup.

### 6.3 Performa

-   Penggunaan CPU dan memori trainer harus rendah.
-   Pemindaian tidak boleh berjalan terus-menerus tanpa kebutuhan.
-   Interval pembaruan status harus dapat dikonfigurasi.

### 6.4 Usability

-   Label fitur harus jelas.
-   Status koneksi dan status cheat mudah dibedakan.
-   Kesalahan harus menjelaskan penyebab dan langkah pemulihan yang
    mungkin dilakukan.

### 6.5 Maintainability

-   Kode dibagi menjadi modul proses, memori, fitur cheat, konfigurasi,
    logging, dan UI.
-   Setiap fitur memiliki antarmuka yang konsisten.
-   Nilai khusus versi game dipisahkan dari logika umum.

## 7. Rancangan Antarmuka Pengguna

### 7.1 Halaman Utama

Komponen: - Nama aplikasi dan versi trainer. - Nama game dan profil
versi yang dipilih. - Status deteksi game. - Tombol Connect /
Disconnect. - Informasi PID dan status koneksi. - Navigasi kategori
cheat.

### 7.2 Kategori

1.  **Resources:** Unlimited Sun, Set Sun, Unlimited Coins, Set Coins,
    Auto Collect.
2.  **Plants:** No Plant Cooldown, Free Plant, Plant Invincibility,
    Instant Recharge, Plant Anywhere.
3.  **Zombies:** Freeze Zombies, No Zombie Attack, One Hit Kill, Kill
    All Zombies, Zombie Invincibility.
4.  **Game:** Stop Timer, Fast Game, Slow Motion, Instant Win, dan fitur
    unlock.
5.  **Settings:** profil game, logging, backup save, dan preferensi
    aplikasi.

### 7.3 Prinsip UI

-   Gunakan checkbox/toggle untuk fitur on/off.
-   Gunakan input numerik untuk Set Sun dan Set Coins.
-   Gunakan tombol aksi untuk Kill All Zombies, Instant Win, dan operasi
    unlock.
-   Fitur yang belum kompatibel harus menampilkan label `Unsupported`
    atau `Unavailable`.
-   Jangan menampilkan status `Enabled` sebelum operasi benar-benar
    berhasil.

## 8. Rekomendasi Teknologi

  -----------------------------------------------------------------------
  Komponen                Teknologi               Keterangan
  ----------------------- ----------------------- -----------------------
  Bahasa                  C++17 atau C++20        Bahasa utama

  Build system            CMake                   Pengelolaan build

  Compiler                MSVC atau MinGW-w64     Kompilasi Windows

  OS API                  Windows API             Deteksi dan pengelolaan
                                                  proses

  UI                      Dear ImGui atau Win32   Antarmuka desktop
                          API                     

  Debugging               Visual Studio Debugger  Analisis saat
                          / x64dbg                pengembangan

  Memory inspection       Tool debugging yang     Observasi proses lokal
                          sesuai                  

  Logging                 spdlog atau logger      Pencatatan aktivitas
                          sederhana               

  Config                  JSON                    Profil dan pengaturan

  Testing                 Catch2 atau GoogleTest  Unit test untuk modul
                                                  non-UI
  -----------------------------------------------------------------------

Windows API yang relevan untuk dipelajari meliputi: -
`CreateToolhelp32Snapshot` dan `Process32First/Process32Next` untuk
enumerasi proses. - `OpenProcess` untuk membuka handle proses dengan hak
akses minimum. - `ReadProcessMemory` dan `WriteProcessMemory` untuk
operasi memori yang memang diperlukan dan telah diverifikasi. -
`CloseHandle` untuk melepas handle. - `GetExitCodeProcess` untuk
memeriksa apakah proses masih berjalan.

Penggunaan API tersebut harus dibungkus dalam modul yang memvalidasi
hasil operasi. Nama API di atas adalah referensi teknologi, bukan
instruksi untuk menggunakan alamat memori tertentu.

## 9. Arsitektur dan Struktur Project

Struktur awal yang disarankan:

``` text
pvz-trainer/
├── CMakeLists.txt
├── README.md
├── docs/
│   ├── PRD.md
│   ├── DESIGN.md
│   └── COMPATIBILITY.md
├── config/
│   └── profiles/
│       └── pvz-original-template.json
├── include/
│   ├── app/
│   ├── process/
│   ├── memory/
│   ├── cheats/
│   ├── config/
│   └── ui/
├── src/
│   ├── main.cpp
│   ├── app/
│   ├── process/
│   ├── memory/
│   ├── cheats/
│   │   ├── resources/
│   │   ├── plants/
│   │   ├── zombies/
│   │   └── game/
│   ├── config/
│   ├── logging/
│   └── ui/
└── tests/
    ├── process/
    ├── config/
    └── cheats/
```

### 9.1 Tanggung Jawab Modul

-   **App:** lifecycle aplikasi dan koordinasi modul.
-   **Process:** deteksi, koneksi, dan status proses game.
-   **Memory:** lapisan operasi memori yang terisolasi dan tervalidasi.
-   **Cheats:** implementasi fitur berdasarkan kategori.
-   **Config:** pembacaan dan validasi profil.
-   **Logging:** pencatatan aktivitas dan error.
-   **UI:** tampilan serta interaksi pengguna.
-   **Tests:** pengujian unit dan pengujian integrasi yang dapat
    dilakukan tanpa mengubah game.

### 9.2 Prinsip Arsitektur

-   UI tidak boleh mengakses Windows API proses secara langsung.
-   Fitur cheat menggunakan interface layanan yang sama.
-   Profil versi game harus diverifikasi sebelum fitur yang bergantung
    pada struktur internal diaktifkan.
-   Operasi yang mengubah data harus memiliki validasi dan mekanisme
    pemulihan yang sesuai.
-   Hindari alamat memori hard-coded tanpa identifikasi versi dan bukti
    kompatibilitas.

## 10. Kompatibilitas

Target awal adalah satu build Plants vs. Zombies Original (2009) pada
Windows. Kompatibilitas dengan edisi GOTY, non-GOTY, Steam, atau
distribusi lain belum boleh diasumsikan sama.

Sebelum mengaktifkan fitur: 1. Identifikasi nama executable. 2.
Identifikasi arsitektur proses. 3. Catat versi/build executable. 4. Uji
fitur satu per satu pada lingkungan lokal. 5. Catat hasil pengujian dan
batasan. 6. Nonaktifkan fitur apabila identitas build tidak cocok.

Profil kompatibilitas sebaiknya mencatat: - Nama profil. - Nama
executable. - Versi/build yang diuji. - Arsitektur. - Fitur yang sudah
diuji. - Status setiap fitur. - Catatan risiko atau keterbatasan.

## 11. Penanganan Error

  -----------------------------------------------------------------------
  Kondisi                             Respons aplikasi
  ----------------------------------- -----------------------------------
  Game tidak ditemukan                Tampilkan `Game Not Detected` dan
                                      tombol Retry

  Akses proses ditolak                Tampilkan pesan permission error

  Game ditutup saat trainer aktif     Hentikan operasi dan lepaskan
                                      handle

  Profil tidak cocok                  Tolak fitur yang bergantung pada
                                      profil

  Operasi baca/tulis gagal            Catat error dan nonaktifkan fitur
                                      terkait

  Input angka tidak valid             Tampilkan validasi tanpa mengirim
                                      operasi

  Backup gagal                        Batalkan perubahan save

  Fitur belum diverifikasi            Tampilkan `Unsupported/Unverified`
  -----------------------------------------------------------------------

## 12. Rencana Pengujian dan Kriteria Penerimaan

### 12.1 Pengujian Umum

-   Trainer dapat dijalankan ketika game belum dibuka.
-   Trainer dapat mendeteksi game ketika game dibuka.
-   Trainer dapat mendeteksi ketika game ditutup.
-   Trainer tidak melakukan operasi jika koneksi tidak valid.
-   UI menampilkan status yang sesuai dengan hasil operasi.
-   Log mencatat error tanpa menyebabkan aplikasi berhenti.

### 12.2 Pengujian Fitur

Setiap fitur harus diuji secara terpisah: - Kondisi awal game dicatat. -
Fitur diaktifkan. - Perubahan yang diharapkan diamati. - Fitur
dinonaktifkan. - Perilaku game diperiksa kembali. - Game ditutup dan
dibuka ulang untuk memeriksa efek samping. - Hasil dicatat per
versi/build.

### 12.3 Kriteria Penerimaan MVP

MVP dianggap selesai jika: 1. Trainer dapat mendeteksi dan menghubungkan
proses game. 2. UI dapat menampilkan status koneksi. 3. Profil versi
dapat dibaca dan divalidasi. 4. Set Sun dan Unlimited Sun berfungsi pada
build target yang telah diverifikasi. 5. Fitur dapat dinonaktifkan
dengan aman. 6. Kegagalan operasi ditangani dan dicatat. 7. Dokumentasi
build dan penggunaan tersedia.

Fitur lain baru dinyatakan selesai setelah melalui pengujian yang sama.
Fitur yang tidak stabil harus ditandai sebagai tidak didukung, bukan
dianggap selesai.

## 13. Roadmap Pengembangan

### Phase 1 --- Project Setup

-   Membuat repository dan konfigurasi CMake.
-   Menentukan compiler dan standar C++.
-   Menyiapkan struktur folder.
-   Menambahkan logging dan konfigurasi dasar.

**Deliverable:** aplikasi C++ minimal yang dapat di-build.

### Phase 2 --- Process Detection

-   Enumerasi proses.
-   Deteksi executable game.
-   Koneksi dan pelepasan handle.
-   Status proses pada UI.

**Deliverable:** trainer dapat mendeteksi game secara konsisten.

### Phase 3 --- Memory Research dan Resource MVP

-   Mempelajari struktur nilai game pada build target.
-   Memvalidasi pembacaan nilai.
-   Mengimplementasikan Set Sun dan Unlimited Sun.
-   Menguji aktivasi, deaktivasi, dan error handling.

**Deliverable:** resource trainer pertama yang teruji.

### Phase 4 --- Plant dan Zombie Features

-   Meneliti mekanisme tanaman dan zombie.
-   Mengimplementasikan fitur satu per satu.
-   Menambahkan pemeriksaan kompatibilitas.
-   Mencatat fitur yang tidak stabil atau tidak didukung.

**Deliverable:** modul tanaman dan zombie dengan status pengujian.

### Phase 5 --- Game dan Unlock Features

-   Meneliti timer dan kecepatan simulasi.
-   Meneliti kondisi penyelesaian level.
-   Mengidentifikasi mekanisme save/profile untuk fitur unlock.
-   Menambahkan backup dan restore sebelum perubahan save.

**Deliverable:** fitur game/unlock yang telah diverifikasi atau daftar
keterbatasan.

### Phase 6 --- UI, QA, dan Release

-   Menyelesaikan UI kategori.
-   Menambahkan pengaturan dan log viewer.
-   Menjalankan regression test.
-   Menulis README dan compatibility notes.
-   Membuat build release.

**Deliverable:** PVZ Trainer versi 1.0 untuk build game yang didukung.

## 14. Risiko dan Mitigasi

  -----------------------------------------------------------------------
  Risiko                  Dampak                  Mitigasi
  ----------------------- ----------------------- -----------------------
  Perbedaan versi         Fitur gagal atau salah  Profil build dan
  executable              sasaran                 validasi kompatibilitas

  Alamat/struktur         Crash atau perilaku     Verifikasi ulang per
  internal berubah        tidak benar             build; jangan gunakan
                                                  alamat universal

  Fitur mengubah state    Game menjadi tidak      Uji satu fitur pada
  yang saling terkait     stabil                  satu waktu

  Save rusak              Progres game hilang     Backup sebelum
                                                  perubahan dan restore

  Hak akses tidak cukup   Koneksi gagal           Minta hak akses minimum
                                                  yang diperlukan

  UI menyatakan fitur     Pengguna salah memahami Status hanya berubah
  aktif padahal gagal     status                  setelah operasi sukses

  Fitur sulit             Jadwal membesar         Prioritaskan MVP dan
  direalisasikan                                  tandai fitur
                                                  eksperimental
  -----------------------------------------------------------------------

## 15. Asumsi dan Pertanyaan Terbuka

Hal berikut harus dipastikan sebelum implementasi fitur yang bergantung
pada versi: - Distribusi game yang digunakan (misalnya GOTY, non-GOTY,
atau Steam). - Nama executable aktual. - Versi/build executable. -
Arsitektur proses. - Sistem operasi Windows yang digunakan untuk
pengujian. - Apakah fitur unlock harus mengubah save/profile atau cukup
berlaku selama sesi berjalan.

Dokumen ini mengasumsikan penggunaan game single-player lokal pada
komputer milik atau yang diizinkan oleh pengguna.

## 16. Definisi Selesai (Definition of Done)

Sebuah fitur dianggap selesai apabila: - Kebutuhan dan perilaku fitur
terdokumentasi. - Implementasi terpisah dan dapat dipelihara. - Versi
game yang didukung telah dicatat. - Fitur telah diuji pada build
target. - Kondisi gagal ditangani. - Aktivasi dan deaktivasi telah
diuji. - Tidak ada klaim kompatibilitas yang belum dibuktikan. -
Dokumentasi penggunaan dan keterbatasan diperbarui.

------------------------------------------------------------------------

## Lampiran A --- Daftar Fitur Keseluruhan

### Resources

-   [ ] Unlimited Sun
-   [ ] Set Sun
-   [ ] Unlimited Coins
-   [ ] Set Coins
-   [ ] Auto Collect

### Plants

-   [ ] No Plant Cooldown
-   [ ] Free Plant
-   [ ] Plant Invincibility
-   [ ] Instant Recharge
-   [ ] Plant Anywhere

### Zombies

-   [ ] Freeze Zombies
-   [ ] No Zombie Attack
-   [ ] One Hit Kill
-   [ ] Kill All Zombies
-   [ ] Zombie Invincibility

### Game

-   [ ] Stop Timer
-   [ ] Fast Game
-   [ ] Slow Motion
-   [ ] Instant Win
-   [ ] Unlock All Plants
-   [ ] Unlock All Mini Games
-   [ ] Unlock Survival Mode

## Lampiran B --- Catatan Implementasi

PRD ini mendefinisikan kebutuhan produk, bukan daftar alamat memori atau
patch siap pakai. Alamat, offset, signature, dan struktur internal game
harus diteliti serta diverifikasi secara terpisah untuk executable
target. Jangan menyalin alamat dari tutorial tanpa memeriksa versi/build
dan hasil pengujiannya.

Mulailah dari process detection dan resource MVP. Setelah fondasi
tersebut stabil, lanjutkan ke fitur lain secara bertahap.
