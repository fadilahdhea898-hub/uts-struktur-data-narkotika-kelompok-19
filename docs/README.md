# UTS Struktur Data - Aplikasi Manajemen Putusan Pidana Khusus PN Jember

## Deskripsi Proyek
Aplikasi berbasis Command Line Interface (CLI) yang dibangun menggunakan bahasa pemrograman Java untuk mengelola, menganalisis, menyaring, mengurutkan, dan mengekspor 50 data putusan perkara pidana khusus (Narkotika) Pengadilan Negeri Jember tahun 2026. Proyek ini dirancang untuk mengimplementasikan struktur data secara mandiri tanpa bergantung pada pustaka koleksi bawaan Java (`java.util`).

---

## Ketentuan & Arsitektur Struktur Data
- **Struktur Data Utama (`SinglyLinkedList`)**: Diimplementasikan secara manual menggunakan simpul (`Node<T>`) untuk menyimpan dan memanipulasi seluruh objek data putusan. Mendukung operasi penyisipan, traversal non-destruktif, penyaringan (*filtering*), penghapusan elemen, serta pengurutan berbasis *Merge Sort* dengan kompleksitas waktu $O(n \log n)$.
- **Struktur Data Sekunder (`Stack`)**: Diimplementasikan dengan prinsip *Last-In, First-Out* (LIFO) melalui `AuditManager` untuk mencatat riwayat aktivitas pengguna (`AuditLog`) secara real-time.
- **Dataset Putusan (`data/putusan-50.csv`)**: Terdiri dari **50 perkara putusan** terverifikasi dengan **16 atribut** per perkara (`nomor_perkara`, `nama_terdakwa`, `jenis_kejahatan`, `pasal`, `tuntutan_bulan`, `vonis_bulan`, `denda`, `subsidair_bulan`, `barang_bukti`, `status_penahanan`, `tingkat_pengadilan`, `nama_hakim`, `nama_jaksa`, `tanggal_putusan`, `status_eksekusi`, dan `catatan`).
- **Data Provenance (`data/data-provenance.csv`)**: Dokumentasi ketertelusuran sumber ekstraksi untuk **800 sel atribut** ($50 \text{ perkara} \times 16 \text{ atribut}$) yang mencatat referensi halaman dokumen putusan (`halaman_pdf`), pihak pengekstrak (`extractor`), peninjau (`reviewer`), serta status verifikasi (`VERIFIED_PDF` / `DIISI_MANUAL`).

---

## Struktur Folder Project
```text
UTS-Struktur-Data/
├── data/
│   ├── data-provenance.csv
│   ├── putusan-50.csv
│   
├── docs/
│   ├── ADR.md
│   ├── AI_USAGE.md
│   ├── CONTRIBUTIONS.md
│   ├── pom.xml
│   ├── README.md
│   └── TRACE.md
└── src/
    ├── main/java/com/uts/strukturdata/
    │   ├── AuditLog.java
    │   ├── AuditManager.java
    │   ├── CSVService.java
    │   ├── Main.java
    │   ├── Node.java
    │   ├── Putusan.java
    │   ├── PutusanService.java
    │   ├── SinglyLinkedList.java
    │   └── Stack.java
    └── test/java/com/uts/strukturdata/
        └── PutusanTest.java
```

---

## Deskripsi Komponen Utama
| Kelas / File | Peran & Tanggung Jawab |
| :--- | :--- |
| `Node.java` | Representasi simpul generik tunggal yang menyimpan elemen data dan referensi ke simpul berikutnya. |
| `SinglyLinkedList.java` | Implementasi struktur data *Singly Linked List* mandiri beserta algoritma pencarian, penghapusan, dan *Merge Sort*. |
| `Stack.java` | Implementasi struktur data tumpukan (*Stack* LIFO) mandiri untuk penyimpanan riwayat log. |
| `Putusan.java` | Model entitas yang merepresentasikan 16 atribut dari setiap berkas putusan pengadilan. |
| `AuditLog.java` & `AuditManager.java` | Pencatatan dan pengelolaan jejak aktivitas sistem menggunakan `Stack`. |
| `CSVService.java` | Layanan pembacaan (*parsing*) dan penulisan (*export*) dataset CSV ke dalam `SinglyLinkedList`. |
| `PutusanService.java` | Layanan logika bisnis meliputi pencarian perkara, penyaringan berdasarkan pasal/vonis, agregasi statistik, dan pengurutan. |
| `Main.java` | Titik masuk (*entry point*) aplikasi yang menyediakan antarmuka menu CLI interaktif. |
| `PutusanTest.java` | Kumpulan skenario *unit testing* berbasis JUnit 5 untuk memvalidasi integritas struktur data. |

---

## Prasyarat & Cara Menjalankan Aplikasi
1. **Prasyarat Sistem**:
   - Java Development Kit (JDK) versi 11 atau lebih baru.
   - Apache Maven 3.6+ (atau Maven bawaan IntelliJ IDEA).
2. **Menjalankan Aplikasi Utama (CLI)**:
   - Buka folder `UTS-Struktur-Data` di **IntelliJ IDEA** atau IDE Java pilihan Anda.
   - Pastikan konfigurasi Project SDK telah mengarah ke JDK 11+.
   - Buka `src/main/java/com/uts/strukturdata/Main.java`, lalu jalankan metode `main()` untuk memulai menu CLI interaktif.
3. **Menjalankan Unit Test**:
   - Melalui terminal di dalam direktori utama proyek:
     ```bash
     mvn test
     ```
   - Atau melalui **IntelliJ IDEA**: buka panel **Maven** di sisi kanan $\rightarrow$ **Lifecycle** $\rightarrow$ klik dua kali **`test`** (atau jalankan langsung kelas `PutusanTest.java`).

---

## Skenario Pengujian yang Terverifikasi (`BUILD SUCCESS`)
Seluruh 5 skenario pengujian pada `PutusanTest.java` telah dijalankan dan lolos tanpa kesalahan (`Tests run: 5, Failures: 0, Errors: 0`):
1. **Pengujian Pencarian Non-Destruktif**: Memastikan operasi pencarian dan penyaringan menghasilkan subset data tanpa merusak atau mengubah susunan `SinglyLinkedList` utama.
2. **Pengujian Fungsi Stack Audit Log (LIFO)**: Memvalidasi urutan masuk dan keluar riwayat aktivitas pada `Stack` sesuai prinsip *Last-In, First-Out*.
3. **Pengujian Invariant & Penghapusan Elemen**: Memastikan konsistensi ukuran (`size`) serta keterhubungan pointer antar-`Node` tetap valid setelah operasi penghapusan elemen di awal, tengah, maupun akhir daftar.
4. **Pengujian Agregasi & Pengurutan Merge Sort**: Memverifikasi akurasi perhitungan statistik (rata-rata, nilai minimum/maksimum) serta kestabilan pengurutan data pada `SinglyLinkedList`.
5. **Pengujian Integrasi Layanan & Dataset**: Memastikan pembacaan dataset `data/putusan-50.csv` berjalan konsisten dengan model data.

---

## Status Akhir Proyek
Seluruh pembaruan dataset putusan 2026 (50 perkara), migrasi `data-provenance.csv` ke direktori `data/`, pencatatan audit log berbasis `Stack`, serta pengujian unit berbasis Maven telah terintegrasi dan terverifikasi penuh.