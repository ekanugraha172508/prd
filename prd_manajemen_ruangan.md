# Product Requirement Document (PRD)
**SIRAJA (Sistem Reservasi Ruangan dan Jadwal) - Instansi Pemerintahan**

## 1. Pendahuluan
### 1.1 Latar Belakang
Pengelolaan dan peminjaman ruang rapat/ruangan di lingkungan instansi pemerintahan seringkali masih dilakukan secara manual atau semi-digital (menggunakan spreadsheet/grup chat). Hal ini sering menimbulkan masalah seperti bentrok jadwal (*double booking*), ketidakefisienan penggunaan fasilitas, serta kesulitan dalam rekam jejak persetujuan (*approval*) dari pihak terkait (Bagian Umum/Perlengkapan). Oleh karena itu, dibutuhkan sebuah Sistem Informasi Manajemen Ruangan berbasis web yang terpusat dan mudah diakses.

### 1.2 Tujuan
- Mendigitalkan dan mengotomatisasi proses peminjaman ruangan.
- Mencegah terjadinya konflik/bentrok jadwal penggunaan ruangan.
- Memberikan transparansi ketersediaan ruangan secara *real-time* kepada seluruh pegawai.
- Mempermudah pelaporan dan evaluasi utilisasi aset/BMN (Barang Milik Negara) berupa ruangan.

### 1.3 Ruang Lingkup
Sistem ini berbasis web dengan antarmuka yang responsif (*mobile-friendly*). Sistem tidak hanya mewadahi kebutuhan internal ekosistem instansi pemerintahan (Pegawai, Pejabat/Atasan, Admin, Resepsionis), namun juga mengakomodasi pihak eksternal (instansi pemerintah lain, pihak swasta, dan perorangan) yang memiliki kebutuhan untuk meminjam ruangan sesuai dengan prosedur yang berlaku.

---

## 2. Pengguna Sistem (User Roles)
Sistem memiliki beberapa peran akses (Role-Based Access Control):
1. **Pemohon:** Pengguna yang mengajukan pemanfaatan/peminjaman ruangan, terbagi menjadi dua sub-kategori:
   - **Internal (Pegawai):** ASN/Non-ASN dalam satu instansi yang memiliki akun *built-in* untuk kebutuhan dinas otomatis.
   - **Eksternal (Tamu/Publik):** Perwakilan dari instansi/kementerian lain, perusahaan swasta, dan perorangan/masyarakat umum. Mereka wajib mengunggah dokumen prasyarat (seperti Surat Permohonan resmi / KTP / NIB) saat pendaftaran atau pengajuan.
2. **Penyetuju (Atasan Langsung - Wajib/Mandatory):** Pejabat struktural/atasan yang memiliki kewenangan penuh dan **diwajibkan** untuk memverifikasi serta menyetujui pengajuan peminjaman dari staf di bawah koordinasinya sebelum pengajuan tersebut diteruskan ke pengelola ruangan.
3. **Pengelola Ruangan (Admin Bagian Umum/BMN):** Administrator yang mengelola master data ruangan, fasilitas, memberikan persetujuan akhir kelayakan peminjaman, serta memantau seluruh jadwal.
4. **Resepsionis/Keamanan:** Staf di lini depan yang bertugas melihat jadwal penyelenggaraan harian untuk membantu mengarahkan tamu/peserta rapat ke ruangan yang tepat.

---

## 3. Kebutuhan Fungsional (Functional Requirements)

### 3.1 Manajemen Akun & Autentikasi
- **FR-AUTH-01:** Sistem menerapkan kerangka *Login Multi-Tipe*. Pengguna **Internal (Pegawai)** diwajibkan *login* menggunakan integrasi *Single Sign-On* (SSO) yang mengacu pada *endpoint* dokumentasi API BBPVP Bekasi ([https://bbpvpbekasi.synology.me:8002/api-docs/#/](https://bbpvpbekasi.synology.me:8002/api-docs/#/)). Sedangkan untuk pendaftar **Eksternal/Publik**, menggunakan modul pengelolaan otentikasi bawaan sistem berbasis verifikasi Email/OTP.
- **FR-AUTH-02:** Sistem mengatur akses menu berdasarkan peran (*Role*) yang ditetapkan oleh Super Admin.

### 3.2 Master Data Ruangan
- **FR-MD-01:** Sistem mendukung pengelolaan **Master Kategori Ruangan** yang bersifat dinamis (bebas ditambah, diubah, atau dihapus oleh Super Admin/Bagian Umum). *Referensinya berupa (namun tidak eksklusif pada): Ruang Rapat, Ruang Kelas, Laboratorium, Workshop, Auditorium, dsb*. Selanjutnya, Admin memetakan data detail tiap ruangan (Nama, Kategori, Gedung, Lantai, Kapasitas, Tarif PNBP).
- **FR-MD-02:** Admin dapat menambahkan inventaris/fasilitas (Proyektor, Mikrofon, Papan Tulis, AC, dsb).
- **FR-MD-03:** Admin dapat mengunggah foto representative dari ruangan tersebut.
- **FR-MD-04:** Admin dapat menetapkan status fungsional ruangan (Tersedia, Sedang Direnovasi, Penugasan Khusus, dsb).

### 3.3 Sistem Peminjaman (Booking System)
- **FR-BK-01:** Sistem memiliki kalender interaktif (Harian/Mingguan/Bulanan) untuk memvisualisasikan ketersediaan ruangan.
- **FR-BK-02:** Pemohon dapat melakukan pencarian *smart-search* berdasarkan kapasitas peserta, waktu kegiatan, dan kebutuhan fasilitas.
- **FR-BK-03:** Form peminjaman dirancang berbasis *Request-Based*. Pemohon **tidak dapat memilih ruangan** mana yang akan digunakan. Pemohon hanya mengisi spesifikasi kebutuhan secara tertulis, namun tetap didukung fitur usulan peminjaman **lebih dari satu slot kebutuhan** (misal butuh 1 ruang inti & 1 ruang transit) pada **tanggal tidak berurutan** sekaligus.
- **FR-BK-04:** Rincian instrumen *Form Pengajuan* memuat kelengkapan parameter *plotting* berikut:
  1. **Informasi Kegiatan:** Judul Acara dan Tingkat Urgensi.
  2. **Waktu Pelaksanaan:** Tanggal Murni (termasuk *Multi-Date*) dan Rentang Waktu (Jam).
  3. **Spesifikasi Teknis:** Kategori Ruang yang diharapkan (Kelas/Lab/Rapat/Aula), Estimasi Jumlah Peserta, dan Kebutuhan Preferensi *Layout* (U-Shape, Theater, dst).
  4. **Kebutuhan Logistik (Checklist):** Proyektor opsional, Papan Tulis, Ekstra *Microphone*, dll.
  5. **Data Pemohon (PIC):** Nama, Nomor WhatsApp aktif (untuk notifikasi bot), dan Asal Bagian/Instansi.
  6. **Lampiran:** Unggah Ekstra (Nota Dinas/Surat Pengajuan resmi).
- **FR-BK-05:** Hak prerogatif penetapan/pemilihan ruangan fisik (*plotting*) diserahkan sepenuhnya kepada Admin BMN (Bagian Umum) untuk meminimalisasi konflik VIP. Kalender sistem hanya merepresentasikan visibilitas sekilas.

### 3.4 Alur Persetujuan (Approval Workflow) & Pembayaran
- **FR-AP-01:** (Jalur Internal) Permohonan otomatis masuk dengan status *Menunggu Persetujuan Atasan*. Atasan langsung **wajib mengevaluasi (Approve/Reject)** terlebih dahulu terkait urgensi kegiatan. Hanya pengajuan yang sudah di-*Approve* Atasan yang akan diteruskan ke antrean Admin BMN/Bagian Umum untuk dilakukan *Plotting* (penentuan ruangan) lalu persetujuan akhir.
- **FR-AP-02:** (Jalur Eksternal) Admin melakukan *plotting* ruangan. Jika yang terpilih adalah ruangan bertarif (PNBP), sistem menerbitkan Tagihan ke Pemohon Eksternal. Setelah Bukti Bayar diunggah, Admin menekan *Approve*.
- **FR-AP-03:** Penyetuju wajib memberikan catatan/keterangan penolakan agar pemohon segera menyadarinya.

### 3.5 Notifikasi
- **FR-NT-01:** Sistem secara otomatis mengirimkan notifikasi Email dan/atau WhatsApp kepada Pemohon terkait perubahan status pengajuannya (Disetujui, Ditolak, Dibatalkan).
- **FR-NT-02:** Sistem mengirimkan notifikasi kepada Admin/Penyetuju ketika terdapat tiket permohonan baru.

### 3.6 Dasbor & Pelaporan (Dashboard & Reporting)
- **FR-RP-01:** *Dashboard Publik (Display Utama):* Terdapat halaman utama (bisa diakses tanpa *login* atau ditampilkan pada layar TV/Lobbi) yang secara *real-time* menampilkan matriks daftar jadwal kegiatan dan penggunaan ruangan khusus pada hari ini.
- **FR-RP-02:** *Dashboard Pribadi (User):* Setelah login, sistem menampilkan Ringkasan Jadwal Pribadi, Ruangan Tersedia, dan Status Permohonan Aktif milik pemohon tersebut.
- **FR-RP-03:** *Pelaporan:* Admin BMN/Umum dapat mengunduh (*export*) laporan log penggunaan ruangan per rentang waktu dalam format PDF atau Excel/CSV untuk keperluan pengawasan dan audit.

### 3.7 Manajemen Migrasi (Import & Export Data)
- **FR-IE-01:** *Import Data Massal:* Admin BMN memiliki utilitas unggah dokumen (.xlsx/.csv) berdasarkan *template* standar sistem untuk mendaftarkan data dalam volume besar, meliputi: Registrasi Pegawai/User baru, Entri *Master Data* Ruangan & Kategorinya, serta daftar Inventaris.
- **FR-IE-02:** *Export Master Data (Backup):* Admin dapat mengunduh ulang (*export*) seluruh himpunan katalog *Master Data* maupun data Pegawai yang tersimpan di sistem (*database*) ke dalam format *spreadsheet* Excel.
- **FR-IE-03:** *Import Riwayat Jadwal:* Sebagai antisipasi transisi sistem, fungsi *Super Admin* diperkenankan untuk mengimpor log/jadwal acara dari *legacy system* (buku besar/excel lama) secara massal (*bulk*) ke dalam kalender SIRAJA demi validitas silang.

---

## 4. Kebutuhan Non-Fungsional (Non-Functional Requirements)
- **Keamanan (Security):** Terdapat kontrol *Log / Audit Trail* untuk melacak setiap aksi (siapa yang menyetujui, siapa yang membatalkan pemesanan). Integrasi sistem wajib mematuhi standar keamanan e-government (SPBE).
- **Kinerja (Performance):** Respon *loading* kalender/jadwal peminjaman maksimal 3 detik.
- **Aksesibilitas (UI/UX):** Menggunakan desain responsif (*Responsive Web Design*) dan *clean interface* berkesan formal namun modern, sehingga optimal dibuka pada Komputer Desktop maupun *Smartphone*.
- **Ketersediaan (Availability):** *Up-time* aplikasi minimal target 99% khususnya pada saat jam pelayanan instansi.

---

## 5. *User Journey* / Alur Penggunaan
1. **Navigasi Sekilas:** Pemohon *Login* -> Hanya bisa melihat agenda kalender publik secara transparan (*Read-Only*) untuk memastikan tanggal tersebut tidak padat.
2. **Pengajuan (Request):** Pemohon mengakses Form Kebutuhan Acara -> Mengisi Tanggal, Jumlah Peserta, Kebutuhan Khusus tanpa *dropdown* Pemilihan Ruangan -> (*Khusus Eksternal:* Mengunggah Surat Pengajuan) -> *Submit*. (Status: *Menunggu Plotting & Persetujuan*).
3. **Plotting & Persetujuan Internal:** Admin BMN mengecek usulan -> Memilih dan menetapkan (*assign*) ruangan yang dinilai cocok dan kosong -> Klik *Approve*.
4. **Pembayaran PNBP (Eksternal):** Jika hasil *plotting* ruangan tersebut berbayar -> Sistem menerbitkan *Invoice* -> Pemohon Eksternal mengunggah bukti -> Admin mengubah status *Approved*. (Jadwal mengunci slot dan tampil di Lobi).
5. **Pelaksanaan:** Menjelang kegiatan, Admin Resepsionis/Keamanan mengecek nama PIC pada *Dashboard* Harian sebagai panduan layanan tamu VIP.
6. **Pembatalan/Penyelesaian (Opsional):** Jika batal, Pemohon dapat menarik (*Cancel*) pengajuan maksimal H-1. Jadwal yang telah lewat otomatis masuk ke arsip *Selesai*.

---

## 6. Asumsi & Batasan (Assumptions & Constraints)
- Sistem berfokus secara eksklusif Manajemen Aset (Ruangan). Pemesanan konsumsi (makan siang/snack) tidak diakomodir oleh sistem ini (berdiri terpisah menyesuaikan SOP di instansi terkait).
- Aksesibilitas sistem harus disiapkan *online* (dapat diakses publik dari luar kantor) karena mengakomodasi pihak eksternal. Katalog/Daftar ruang rapat yang bersifat pimpinan tinggi (sangat rahasia) secara sistematis disembunyikan (*hidden*) dari publik luar dan hanya dapat dilihat oleh level pejabat *internal*.
