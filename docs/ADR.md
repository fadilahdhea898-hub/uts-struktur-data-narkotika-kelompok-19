# Architectural Decision Records (ADR)
**Proyek:** Aplikasi Pengelolaan & Analisis Dataset Putusan Pidana Khusus PN Jember (Java CLI)  
**Repositori:** `uts-struktur-data-narkotika-kelompok-19`  
**Status Dokumen:** Final / Accepted (2026)

---

## ADR-001: Pemilihan `SinglyLinkedList<T>` Generik Mandiri sebagai Struktur Data Utama
- **Status:** Accepted
- **Tanggal:** 2026
- **Konteks:**  
  Aplikasi membutuhkan wadah penyimpanan dinamis untuk menampung 50 perkara putusan pengadilan (`Putusan`) dari dataset CSV serta mendukung penambahan, penghapusan, traversal, dan penyaringan data secara fleksibel tanpa bergantung pada pustaka koleksi bawaan Java (`java.util.ArrayList` atau `java.util.LinkedList`).
- **Alternatif yang Dipertimbangkan:**
    1. **Array Statis (`Putusan[]`):** Memiliki akses indeks cepat ($O(1)$), tetapi kapasitasnya tetap sehingga membutuhkan penyalinan ulang array (*resizing* $O(n)$) setiap kali kapasitas penuh dan pergeseran elemen saat penghapusan di tengah.
    2. **Doubly Linked List:** Mendukung traversal dua arah, tetapi membutuhkan dua pointer (`prev` dan `next`) per simpul sehingga konsumsi memori lebih besar dan pembaruan referensi lebih kompleks saat penyisipan/penghapusan.
    3. **Singly Linked List (`SinglyLinkedList<T>`) Mandiri dengan Pointer `head` dan `tail`:** Hanya membutuhkan satu referensi `next` per simpul (`Node<T>`) dengan penyisipan akhir (*append*) berbiaya konstan.
- **Keputusan:**  
  Mengimplementasikan **`SinglyLinkedList<T>` generik buatan sendiri** yang dilengkapi pointer `head`, `tail`, dan penghitung `size`.
- **Konsekuensi:**
    - **Positif (+):** Penyisipan data sekuensial saat memuat 50 baris CSV berjalan dalam kompleksitas waktu $O(1)$ melalui `addLast()`, alokasi memori bersifat dinamis sesuai jumlah record, serta bebas dari ketergantungan `java.util`.
    - **Negatif (-):** Akses elemen berdasarkan indeks (`get(index)`) membutuhkan traversal linear $O(n)$ dari `head`.

---

## ADR-002: Pemilihan Struktur Data `Stack<T>` (LIFO) untuk Pencatatan Audit Log
- **Status:** Accepted
- **Tanggal:** 2026
- **Konteks:**  
  Setiap aktivitas pengguna pada antarmuka CLI (pemuatan dataset, pencarian nomor perkara, penyaringan minimal vonis, pengurutan durasi vonis, dan penghitungan statistik agregasi) harus terekam sebagai jejak audit (`AuditLog`) dan dapat ditinjau dengan urutan **aktivitas paling baru berada di urutan teratas**.
- **Alternatif yang Dipertimbangkan:**
    1. **Queue (FIFO):** Menampilkan aktivitas paling lama terlebih dahulu, sehingga pengguna harus menelusuri seluruh riwayat lama untuk melihat aksi terakhir.
    2. **SinglyLinkedList dengan Penyisipan di Akhir:** Membutuhkan traversal ke ujung atau pembalikan urutan setiap kali riwayat terbaru ingin ditampilkan.
    3. **Stack (`Stack<T>`) Berbasis Simpul (LIFO):** Menempatkan setiap catatan baru tepat di puncak tumpukan (`top`).
- **Keputusan:**  
  Mengimplementasikan struktur data **`Stack<T>` generik mandiri (Last-In, First-Out)** yang dikelola melalui `AuditManager`.
- **Konsekuensi:**
    - **Positif (+):** Operasi pencatatan log baru (`push`) dan pembacaan aktivitas terakhir (`peek`/`pop`) berjalan dalam waktu konstan $O(1)$, dan urutan tampil otomatis kronologis terbalik (terbaru ke terlama).
    - **Negatif (-):** Penelusuran riwayat lama di dasar tumpukan membutuhkan traversal berurutan dari `top` ke bawah ($O(n)$) secara non-destruktif agar isi riwayat tidak terhapus saat dilihat.

---

## ADR-003: Strategi Operasi Non-Destruktif & Pengurutan *Merge Sort* ($O(n \log n)$)
- **Status:** Accepted
- **Tanggal:** 2026
- **Konteks:**  
  Pengguna sering melakukan pencarian berdasarkan nomor perkara, penyaringan perkara berdasarkan batas minimal vonis (`filterByMinimalVonis`), maupun pengurutan durasi vonis (`ASC`/`DESC`). Jika operasi tersebut memodifikasi langsung rantai simpul utama, maka urutan asli 50 putusan dari dataset akan hilang.
- **Alternatif yang Dipertimbangkan:**
    1. **In-Place Mutation (Modifikasi Langsung):** Mengubah pointer atau menghapus simpul langsung pada `SinglyLinkedList` utama. Berisiko menghilangkan data asli dan merusak konsistensi tampilan berikutnya.
    2. **Bubble Sort / Selection Sort pada Salinan List:** Mudah ditulis, tetapi memiliki kompleksitas waktu kuadratik $O(n^2)$ yang kurang optimal ketika jumlah record bertambah besar.
    3. **Operasi Non-Destruktif dengan Pengurutan *Merge Sort*:** Mengembalikan objek `SinglyLinkedList` baru untuk setiap hasil query/filter/sort.
- **Keputusan:**  
  Menerapkan prinsip **non-destruktif** pada seluruh layanan di `PutusanService` serta menggunakan algoritma **Merge Sort** berbasis pembagian pointer tengah (*fast-slow pointer*) untuk fitur pengurutan durasi vonis.
- **Konsekuensi:**
    - **Positif (+):** Integritas dan urutan asli dataset utama tetap terjaga 100% selama sesi aplikasi berjalan, serta pengurutan memiliki kompleksitas waktu terjamin $O(n \log n)$ dan bersifat stabil (*stable sort*).
    - **Negatif (-):** Membutuhkan alokasi simpul tambahan sementara sebesar $O(n)$ untuk menampung daftar hasil query atau pengurutan.

---

## ADR-004: Standarisasi Parsing CSV 16 Atribut & Pemusatan Data Provenance
- **Status:** Accepted
- **Tanggal:** 2026
- **Konteks:**  
  Dataset putusan PN Jember tahun 2026 (`data/putusan-50.csv`) memuat 50 perkara dengan 16 atribut per perkara, di mana beberapa atribut teks (seperti `pasal` dan `barang_bukti`) mengandung tanda baca koma di dalam isinya. Selain itu, ketertelusuran sumber halaman dokumen PDF untuk 800 sel data (`data/data-provenance.csv`) harus terorganisasi dengan jelas.
- **Alternatif yang Dipertimbangkan:**
    1. **`String.split(",")` Sederhana:** Gagal memisahkan kolom secara akurat apabila teks pasal atau barang bukti mengandung tanda koma.
    2. **Menyimpan File Provenance Terpisah di Folder Dokumentasi (`docs/`):** Memisahkan file data mentah dari file audit datanya.
    3. **Parser CSV Sadar Tanda Kutip (*Quote-Aware Parser*) & Penyatuan Aset Data di `data/`:** Membaca karakter demi karakter untuk mengabaikan koma di dalam tanda kutip ganda (`"..."`) dan memusatkan seluruh file `.csv` di direktori `data/`.
- **Keputusan:**  
  Mengimplementasikan parser CSV sadar tanda kutip pada `CSVService.java` dengan validasi konversi tipe numerik (`int`/`long`), serta menempatkan `putusan-50.csv` dan `data-provenance.csv` bersama-sama di dalam folder `data/`.
- **Konsekuensi:**
    - **Positif (+):** Seluruh 50 baris dan 16 kolom terbaca akurat tanpa pergeseran indeks atribut, serta seluruh aset dataset dan audit provenance terpusat rapi di satu direktori (`data/`).
    - **Negatif (-):** Logika pembacaan baris pada `CSVService` sedikit lebih panjang dibanding pemanggilan `split(",")` biasa.