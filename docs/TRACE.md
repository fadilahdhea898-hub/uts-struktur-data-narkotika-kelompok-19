# Requirements & Architecture Traceability Matrix (`TRACE.md`)
**Proyek:** Aplikasi Pengelolaan & Analisis Dataset Putusan Pidana Khusus PN Jember (Java CLI)  
**Repositori:** `uts-struktur-data-narkotika-kelompok-19`  
**Status Verifikasi Keseluruhan:** 100% Verified (`mvn test` — 5/5 Tests Passed, `BUILD SUCCESS`)

---

## 1. Matriks Keterlacakan End-to-End (Kebutuhan $\rightarrow$ ADR $\rightarrow$ Kode $\rightarrow$ Pengujian)

| ID Req | Kebutuhan Sistem & Spesifikasi | Referensi ADR | Komponen Kelas & Method Implementasi | Aset Data / UI Terkait | Skenario Pengujian (`PutusanTest.java`) | Referensi PR & PIC | Status |
| :--- | :--- | :---: | :--- | :--- | :--- | :--- | :---: |
| **REQ-01** | **Struktur Data Utama Mandiri:** Menyimpan data putusan secara dinamis tanpa pustaka koleksi bawaan `java.util`. | `ADR-001` | `Node.java`<br>`SinglyLinkedList.java`<br>• `addLast()`, `addFirst()`<br>• `get()`, `remove()`<br>• `size()`, `isEmpty()` | Memori Utama Aplikasi | `testInvariantDanPenghapusanElemen()`<br>(Validasi `size` & keterhubungan pointer `head`/`tail` setelah penyisipan & penghapusan) | Issue `#6`, `#7`<br>PR `#12`, `#13`<br>(Dhea Fadilah) | **Verified** |
| **REQ-02** | **Struktur Data Sekunder Audit Log (LIFO):** Mencatat setiap operasi pengguna secara *real-time* dan menampilkan aksi terbaru lebih dulu. | `ADR-002` | `AuditLog.java`<br>`Stack.java` (`push`, `pop`, `peek`)<br>`AuditManager.java` (`record`, `tampilkanRiwayat`) | Menu Riwayat Aktivitas di `Main.java` | `testFungsiStackAuditLogLIFO()`<br>(Validasi prinsip *Last-In, First-Out* pada `Stack` & `AuditManager`) | PR `#8`<br>(Rizki) | **Verified** |
| **REQ-03** | **Impor Dataset 50 Putusan & Provenance:** Membaca 50 record perkara dengan 16 atribut secara akurat termasuk kolom bertanda kutip. | `ADR-004` | `CSVService.java`<br>• `loadPutusanFromCSV()`<br>• *Quote-aware parser* (`"..."`) | `data/putusan-50.csv`<br>`data/data-provenance.csv` | `testIntegrasiLayananDanDataset()`<br>(Validasi pembacaan 50 record & 16 atribut dataset 2026) | Issue `#1`<br>PR `#2`, `#11`<br>(Rizki) | **Verified** |
| **REQ-04** | **Pencarian Non-Destruktif:** Mencari perkara berdasarkan nomor perkara (*substring* & *case-insensitive*) tanpa mengubah daftar utama. | `ADR-002`<br>`ADR-003` | `PutusanService.java`<br>• `cariByNomorPerkara()` / `searchByNomorPerkara()` | Menu Pencarian di `Main.java` | `testPencarianNonDestruktif()`<br>(Validasi hasil pencarian & keutuhan urutan `SinglyLinkedList` utama) | PR `#9`<br>(Rizki) | **Verified** |
| **REQ-05** | **Penyaringan Minimal Vonis:** Menyaring perkara dengan durasi vonis $\ge$ ambang bulan tertentu dan menampilkannya dalam tabel CLI terformat. | `ADR-003` | `PutusanService.java`<br>• `filterByMinimalVonis()` | Tampilan Tabel CLI di `Main.java` | `testPencarianNonDestruktif()`<br>(Validasi penyaringan subset data tanpa mutasi list asal) | PR `#15`<br>(Dhea Fadilah) | **Verified** |
| **REQ-06** | **Pengurutan Durasi Vonis (*Merge Sort*):** Mengurutkan data putusan (`ASC`/`DESC`) dalam kompleksitas $O(n \log n)$ secara stabil dan non-destruktif. | `ADR-003` | `SinglyLinkedList.java`<br>`PutusanService.java`<br>• `sort()` / `sortByDurasiVonis()` | Menu Pengurutan di `Main.java` | `testAgregasiDanPengurutanMergeSort()`<br>(Validasi urutan menaik/menurun & stabilitas *Merge Sort*) | PR `#14`<br>(Dhea Fadilah) | **Verified** |
| **REQ-07** | **Statistik & Agregasi Vonis:** Menghitung total perkara, total bulan, rata-rata, nilai minimum, dan maksimum dalam satu kali lintasan ($O(n)$). | `ADR-001` | `PutusanService.java`<br>• `cetakStatistik()` / `hitungStatistikAgregasi()` | Output Statistik di `Main.java` | `testAgregasiDanPengurutanMergeSort()`<br>(Validasi akurasi nilai rata-rata, min, maks, dan penanganan list kosong) | PR `#10`<br>(Rizki) | **Verified** |
| **REQ-08** | **Ekspor Hasil Olahan ke CSV:** Menyimpan hasil penyaringan atau pengurutan kembali ke file CSV berformat standar 16 kolom. | `ADR-004` | `CSVService.java`<br>• `exportToCSV()` | `data/putusan-50-export.csv` | `testIntegrasiLayananDanDataset()`<br>(Validasi penulisan & pembacaan ulang file ekspor CSV) | PR `#11`<br>(Rizki) | **Verified** |
| **REQ-09** | **Antarmuka CLI Interaktif & Integrasi Sistem:** Menyediakan navigasi menu berbasis konsol yang menghubungkan seluruh layanan dan audit log. | `ADR-001`–`004` | `Main.java`<br>• `main()` & loop menu `Scanner` | Konsol Terminal / CLI | `testIntegrasiLayananDanDataset()`<br>(Validasi alur integrasi antar-layanan) | PR `#11`<br>(Dhea Fadilah & Rizki) | **Verified** |

---

## 2. Keterlacakan Dataset & Provenance (`data/`)

| Aset File | Lokasi | Jumlah Baris | Keterangan Keterlacakan |
| :--- | :--- | :---: | :--- |
| **Dataset Utama Putusan** | `data/putusan-50.csv` | 50 record (+1 *header*) | Memuat 50 perkara pidana khusus (Narkotika) PN Jember tahun 2026 dengan 16 atribut terverifikasi (`nomor_perkara` s.d. `catatan`). |
| **Matriks Provenance Sel** | `data/data-provenance.csv` | 800 baris (+1 *header*) | Memetakan ketertelusuran sumber halaman PDF (`halaman_pdf`), pengekstrak, peninjau, dan status validasi (`VERIFIED_PDF` / `DIISI_MANUAL`) untuk setiap sel ($50 \text{ perkara} \times 16 \text{ atribut}$). |

---

## 3. Ringkasan Hasil Verifikasi Pengujian Otomatis (`mvn test`)
Seluruh item dalam matriks keterlacakan di atas telah diverifikasi melalui *build* Maven (`docs/pom.xml`) dengan hasil berikut:
- **Test Suite:** `com.uts.strukturdata.PutusanTest`
- **Total Pengujian Dijalankan:** 5 skenario uji (`Tests run: 5, Failures: 0, Errors: 0, Skipped: 0`)
- **Status Build:** `BUILD SUCCESS`
- **Status Akhir Kolaborasi:** Seluruh fitur utama, struktur data mandiri (`SinglyLinkedList` dan `Stack`), pencatatan audit log, dataset 2026 beserta 800 baris provenance, dokumentasi teknis (`ADR.md`, `TRACE.md`, `AI_USAGE.md`, `CONTRIBUTIONS.md`, `README.md`), dan pengujian unit telah terintegrasi penuh pada branch `main`.