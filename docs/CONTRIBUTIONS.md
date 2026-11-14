# Matriks Kontribusi Tim (`CONTRIBUTIONS.md`)
**Proyek:** Aplikasi Pengelolaan & Analisis Dataset Putusan Pidana Khusus PN Jember (Java CLI)  
**Repositori:** `uts-struktur-data-narkotika-kelompok-19`  
**Kelompok:** 19

---

## 1. Profil Anggota Kelompok

| No | Nama Lengkap | NIM | Akun GitHub | Peran Utama dalam Proyek |
| :-: | :--- | :---: | :--- | :--- |
| **1** | **Dhea Fadillah Akbar** | `202510370110078` | `@fadilahdhea898-hub` | **Lead Data Extractor, Core Data Structures Engineer, & Technical Architect**<br>• Ekstraksi 50 berkas putusan PDF PN Jember tahun 2026 (`data/putusan-50.csv`).<br>• Penyusunan 800 baris matriks ketertelusuran data (`data/data-provenance.csv`).<br>• Implementasi seluruh struktur data inti mandiri (`Node<T>`, `SinglyLinkedList<T>`, `Stack<T>`).<br>• Penyusunan dokumen arsitektur & teknis (`ADR.md`, `TRACE.md`, `AI_USAGE.md`, `README.md`). |
| **2** | **Muhammad Rizky Adrianto** | `202510370110073` | `@bishamon315` | **Data Provenance Reviewer, Service & Audit Engineer, & QA Testing Lead**<br>• Peninjauan (*review*) dan validasi silang 50 dataset putusan beserta 800 entri *data provenance*.<br>• Implementasi layanan logika bisnis (`PutusanService`), pencatatan audit (`AuditManager`, `AuditLog`), serta layanan CSV (`CSVService`).<br>• Penyusunan & eksekusi skenario pengujian unit JUnit 5 (`PutusanTest.java` — 5/5 passed).<br>• Pembaruan & verifikasi dokumentasi kontribusi tim (`CONTRIBUTIONS.md`). |

---

## 2. Rincian Pembagian Tugas Berdasarkan Komponen Proyek

### A. Kurasi Dataset & Audit Provenance (`data/`)
| Aset File | Dhea Fadillah Akbar (`202510370110078`) | Muhammad Rizky Adrianto (`202510370110073`) | Status |
| :--- | :--- | :--- | :---: |
| **`data/putusan-50.csv`**<br>*(50 Perkara × 16 Atribut)* | **Extractor:** Mengekstrak 16 atribut dari 50 dokumen putusan PDF PN Jember tahun 2026, menormalisasi satuan numerik (`tuntutan_bulan`, `vonis_bulan`, `denda`, `subsidair_bulan`), dan memformat CSV standar. | **Reviewer & Verifier:** Melakukan pemeriksaan ulang (*cross-check*) seluruh 50 baris data terhadap berkas sumber putusan dan memvalidasi kesiapan pembacaannya oleh parser Java. | **Verified** |
| **`data/data-provenance.csv`**<br>*(800 Baris Provenance)* | **Extractor:** Menyusun pemetaan asal halaman (`halaman_pdf`) dan metadata ekstraksi untuk seluruh 800 sel atribut ($50 \times 16$). | **Reviewer:** Memverifikasi keakuratan referensi halaman PDF dan status validasi (`VERIFIED_PDF` / `DIISI_MANUAL`) pada seluruh 800 baris provenance. | **Verified** |

### B. Implementasi Struktur Data & Layanan Java (`src/main/java/com/uts/strukturdata/`)
| Komponen / Kelas | Penanggung Jawab Utama | Peninjau (*Code Reviewer*) | Deskripsi Kontribusi Teknis |
| :--- | :---: | :---: | :--- |
| `Node.java` | **Dhea Fadillah Akbar** | Muhammad Rizky Adrianto | Implementasi simpul generik `Node<T>` sebagai fondasi rantai pointer untuk *linked list* dan *stack*. |
| `SinglyLinkedList.java` | **Dhea Fadillah Akbar** | Muhammad Rizky Adrianto | Implementasi struktur data dinamis `SinglyLinkedList<T>` mandiri (`addFirst`, `addLast` $O(1)$, `get`, `remove`, `size`, `isEmpty`, dan *Merge Sort* $O(n \log n)$). |
| `Stack.java` | **Dhea Fadillah Akbar** | Muhammad Rizky Adrianto | Implementasi struktur data tumpukan `Stack<T>` mandiri berbasis prinsip *Last-In, First-Out* (`push`, `pop`, `peek`). |
| `Putusan.java` | **Dhea Fadillah Akbar** | Muhammad Rizky Adrianto | Pemodelan entitas 16 atribut perkara putusan beserta enkapsulasi *getter* dan pemformatan output. |
| `PutusanService.java` | **Muhammad Rizky Adrianto** | Dhea Fadillah Akbar | Implementasi logika bisnis non-destruktif: pencarian nomor perkara, penyaringan minimal vonis, pengurutan durasi vonis, dan statistik agregasi ($O(n)$). |
| `AuditLog.java` & `AuditManager.java` | **Muhammad Rizky Adrianto** | Dhea Fadillah Akbar | Implementasi pencatatan jejak aktivitas pengguna (*audit trail*) ke dalam `Stack` serta penampilannya dari aksi terbaru ke terlama. |
| `CSVService.java` | **Muhammad Rizky Adrianto** | Dhea Fadillah Akbar | Implementasi *quote-aware parser* untuk membaca `putusan-50.csv` serta fitur ekspor hasil olahan ke CSV. |
| `Main.java` | **Kolaborasi Tim** | Silang Antar Anggota | Integrasi menu antarmuka CLI interaktif yang menghubungkan `CSVService`, `PutusanService`, dan `AuditManager`. |

### C. Pengujian Otomatis & Dokumentasi (`src/test/` & `docs/`)
| Komponen / File | Penanggung Jawab Utama | Peninjau (*Reviewer*) | Deskripsi Kontribusi |
| :--- | :---: | :---: | :--- |
| `PutusanTest.java` | **Muhammad Rizky Adrianto** | Dhea Fadillah Akbar | Menyusun dan memvalidasi 5 skenario *unit test* JUnit 5 (pencarian non-destruktif, LIFO `Stack`, invarian & penghapusan node, agregasi & *Merge Sort*, integrasi dataset) hingga berstatus `BUILD SUCCESS`. |
| `docs/ADR.md` & `docs/TRACE.md` | **Dhea Fadillah Akbar** | Muhammad Rizky Adrianto | Menyusun *Architecture Decision Records* (`ADR-001` s.d. `ADR-004`) dan matriks keterlacakan kebutuhan-kode-pengujian (`TRACE.md`). |
| `docs/AI_USAGE.md` & `docs/README.md` | **Dhea Fadillah Akbar** | Muhammad Rizky Adrianto | Menyusun log transparan pemanfaatan Claude dan Gemini dari awal hingga akhir proyek serta panduan lengkap aplikasi. |
| `docs/CONTRIBUTIONS.md` | **Muhammad Rizky Adrianto** | Dhea Fadillah Akbar | Memperbarui dan memvalidasi matriks pembagian tugas, peran ekstraksi-review data, serta status kolaborasi tim. |

---

## 3. Alur Kolaborasi Git & Pernyataan Keseimbangan Kontribusi
- **Alur Kerja (*Feature Branch & Pull Request*):** Seluruh pengembangan dilakukan pada *branch* fitur terpisah (`feature/model-putusan`, `feature/singly-linked-list`, `feature/putusan-search`, `feature/putusan-sorter`, `feature/putusan-display-filter`, `feature/putusan-aggregator`, serta pembaruan dokumentasi). Setiap *Pull Request* wajib ditinjau dan disetujui (*Peer Reviewed*) oleh rekan tim sebelum digabungkan ke branch `main`.
- **Proporsi Kontribusi:** Seluruh tahapan proyek—mulai dari ekstraksi dan peninjauan 50 dataset serta 800 baris provenance, perancangan dan implementasi struktur data mandiri (`SinglyLinkedList`, `Node`, `Stack`), layanan bisnis (`PutusanService`, `AuditManager`, `CSVService`), pengujian unit JUnit (`mvn test`), hingga penyusunan dokumentasi teknis—dikerjakan secara kolaboratif dan seimbang (**50% : 50%**) oleh **Dhea Fadillah Akbar (`202510370110078`)** dan **Muhammad Rizky Adrianto (`202510370110073`)**.