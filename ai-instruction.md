# AI Instructions — Ecommerce Apps

Dokumen ini menjadi instruksi kerja bagi AI coding agent yang melakukan audit, analisis, perbaikan, dan verifikasi pada repository ini.

## 1. Project Context

- Repository: `gantulu/app`
- Project: Ecommerce Apps
- Frontend target: React + Vite + TypeScript
- Backend target: Supabase PostgreSQL + Supabase Edge Functions
- Payment gateway V1: Duitku POP Sandbox
- Checkout: guest checkout only; tidak ada customer login pada V1
- Currency: IDR
- Scope: storefront responsif; admin dashboard tidak termasuk V1

**Source of truth:** keputusan requirement yang telah disetujui, implementasi aktual dalam repository, dokumentasi resmi teknologi/provider, lalu README.md. Jangan menganggap rencana sebagai fitur yang sudah diimplementasikan.

## 2. Mission

Secara proaktif dan berulang:

1. Audit repository dan kondisi branch terkini.
2. Analisis masalah, ketidaksesuaian requirement, bug, risiko keamanan, dan technical debt.
3. Tentukan perbaikan paling kecil yang menyelesaikan masalah tanpa memperluas scope.
4. Implementasikan perbaikan yang aman dan didukung bukti.
5. Jalankan verifikasi yang relevan.
6. Perbarui dokumentasi bila perilaku atau arsitektur berubah.
7. Integrasikan perubahan ke `main` hanya sesuai merge policy pada dokumen ini.

Jangan berhenti pada daftar rekomendasi untuk temuan yang jelas, terukur, dan aman diperbaiki. Lakukan perbaikan otomatis dalam batas kewenangan berikut.

## 3. Required Workflow

Gunakan urutan berikut untuk setiap siklus:

### A. Audit
- Periksa branch, commit terbaru, working tree, struktur file, konfigurasi, dependency, dan status CI.
- Baca README.md, dokumen instruksi, requirement terkait, serta implementasi yang terdampak.
- Periksa apakah ada perubahan pengguna yang belum di-commit. Jangan menimpa atau menghapusnya.
- Identifikasi test, lint, typecheck, build, migration, dan security checks yang benar-benar tersedia.

### B. Analyze
- Cocokkan implementasi aktual dengan requirement yang disetujui.
- Telusuri akar masalah; jangan hanya memperbaiki gejala.
- Klasifikasikan temuan: Critical, High, Medium, atau Low.
- Catat bukti berupa path file, simbol, perilaku, hasil command, atau test yang gagal.
- Bedakan fakta terverifikasi dari asumsi.

### C. Plan
- Pilih perbaikan terkecil dengan dampak dan risiko paling rendah.
- Pertahankan arsitektur, naming, conventions, dan keputusan V1 yang sudah disetujui.
- Jika requirement saling bertentangan atau keputusan produk belum ada, jangan menebak. Ajukan pertanyaan atau buat laporan blocker.
- Hindari refactor luas yang tidak diperlukan untuk memperbaiki temuan.

### D. Implement
- Perbaiki temuan yang berada dalam batas auto-fix.
- Tambahkan atau perbarui test untuk perubahan perilaku.
- Perbarui README atau dokumentasi terkait jika memang diperlukan.
- Jangan mengubah file atau modul yang tidak berkaitan.
- Jangan menambahkan dependency tanpa alasan teknis yang jelas.

### E. Verify
Jalankan pemeriksaan yang tersedia dan relevan, sekurang-kurangnya:
- Typecheck.
- Lint.
- Unit/integration tests.
- Production build.
- Migration/schema validation jika perubahan database dilakukan.
- Security checks untuk perubahan autentikasi, otorisasi, data, atau payment.

Jangan mengarang hasil verifikasi. Jika suatu pemeriksaan tidak tersedia atau tidak dapat dijalankan, laporkan secara eksplisit.

### F. Report
Setiap siklus wajib menghasilkan ringkasan:
- Temuan dan tingkat severity.
- Root cause.
- File yang diubah.
- Perbaikan yang dilakukan.
- Test/check yang dijalankan beserta hasilnya.
- Risiko tersisa dan pekerjaan yang diblokir.
- Commit dan status integrasi ke `main`.

## 4. Automatic-Fix Authority

### Boleh diperbaiki otomatis
Dengan syarat perubahan memiliki dasar yang jelas dan verifikasi memadai:

- Typo, formatting, dan dokumentasi yang tidak ambigu.
- Type errors, lint errors, dan bug lokal dengan expected behavior yang sudah ditentukan.
- Broken imports, dead code yang terbukti tidak digunakan, dan konfigurasi non-destruktif.
- UI defects yang bertentangan dengan spesifikasi UX yang telah disetujui.
- Test yang kurang untuk perilaku yang requirement-nya sudah jelas.
- Dependency patch/minor yang kompatibel, setelah memeriksa changelog dan menjalankan verifikasi.
- Perbaikan aksesibilitas dan responsive behavior yang tidak mengubah requirement produk.

### Wajib dihentikan dan meminta persetujuan
Jangan melakukan perubahan otomatis sebelum ada keputusan eksplisit untuk:

- Mengubah requirement, business rules, scope V1, atau keputusan yang sudah dikunci.
- Mengubah skema database secara breaking atau melakukan migration destruktif.
- Menghapus data, tabel, kolom, bucket, file pengguna, atau histori transaksi.
- Mengubah inventory lifecycle, order/payment state machine, nominal transaksi, refund, atau rekonsiliasi.
- Mengubah integrasi Duitku, signature/authentication, callback semantics, credential, atau payment flow secara material.
- Mengubah authorization, RLS, akses publik, privilege, atau batas keamanan.
- Menambahkan customer authentication atau admin dashboard yang dikecualikan dari V1.
- Melakukan deployment, mengubah project Supabase, secret, environment produksi, domain, billing, atau layanan eksternal.
- Melakukan perubahan besar lintas modul atau keputusan arsitektur yang belum disetujui.

Untuk temuan Critical/High yang membutuhkan tindakan tersebut, dokumentasikan risiko dan rekomendasi; jangan melakukan workaround yang melemahkan keamanan.

## 5. Git and Main-Branch Integration Policy

Tujuan integrasi adalah menjaga `main` selalu dalam keadaan terverifikasi.

1. Jangan force-push, rewrite history, menghapus branch pengguna, atau menimpa perubahan yang tidak terkait.
2. Jangan menonaktifkan branch protection, required reviews, status checks, atau repository rules.
3. Periksa aturan branch dan CI sebelum memilih metode integrasi.
4. Untuk perubahan Low-risk dan terisolasi, direct commit ke `main` hanya boleh dilakukan jika repository memang mengizinkannya dan seluruh verifikasi yang relevan lulus.
5. Untuk perubahan yang membutuhkan review atau pemeriksaan CI, buat branch dan pull request menuju `main`. Aktifkan auto-merge hanya jika repository mendukungnya, seluruh required checks lulus, tidak ada konflik, dan kebijakan repository mengizinkan merge tanpa review tambahan.
6. Jangan pernah bypass required review, required status checks, atau protection rules demi menyelesaikan merge otomatis.
7. Jika merge tidak diizinkan, checks gagal, ada konflik, atau status tidak dapat diverifikasi, hentikan integrasi. Biarkan perubahan di branch/PR dan laporkan blocker.
8. Setelah merge, verifikasi bahwa commit hasil integrasi benar-benar berada pada `main` dan CI tetap sehat.

**Tidak ada jaminan semua perubahan dapat langsung di-merge.** Keamanan, aturan repository, dan hasil verifikasi selalu lebih utama daripada otomatisasi merge.

## 6. Project-Specific Invariants

Jangan melanggar aturan V1 berikut tanpa persetujuan:

- Guest checkout only; tidak ada customer login.
- Currency IDR only.
- Duitku POP Sandbox only.
- Product catalogue dikelola melalui Supabase Database.
- Kategori sederhana.
- Varian produk: size dan color; harga varian sama dengan harga produk induk.
- Stock divalidasi saat checkout, dikurangi ketika order dibuat, dan dipulihkan untuk pembayaran yang terkonfirmasi failed/expired melalui proses duplicate-safe.
- Ongkos kirim ditentukan manual per order oleh operator/backend.
- Order tracking menggunakan status internal.
- Refund dilakukan manual di luar sistem; hasilnya dicatat melalui backend.
- Admin dashboard tidak termasuk V1.
- Operator-only actions wajib memiliki otorisasi backend.
- Browser redirect/return page bukan bukti pembayaran berhasil.
- Backend adalah otoritas untuk harga, stok, order, dan status pembayaran.
- Secret dan service-role credentials tidak boleh masuk ke frontend bundle atau repository.

## 7. Technical Standards

- Gunakan dokumentasi resmi sebagai sumber kebenaran untuk React, Vite, TypeScript, Supabase, dan Duitku.
- Jangan mengarang endpoint, request fields, response fields, status, signature formula, atau environment variable.
- Jangan mempercayai input browser untuk nominal, stok, otorisasi, atau status pembayaran.
- Terapkan validasi server-side, idempotency, dan transaksi database untuk operasi kritis.
- Callback payment harus diverifikasi dan diproses secara duplicate-safe.
- Jangan mencatat secret, token, atau data pribadi/payment yang tidak diperlukan ke log.
- Ikuti conventions dan dependency yang sudah ada; jangan memaksakan pola baru tanpa kebutuhan.
- Jangan mengklaim fitur selesai hanya karena dokumentasi atau stub sudah dibuat.

## 8. Repository Baseline Awareness

Pada saat instruksi ini dibuat, repository diketahui memiliki README.md sebagai dokumentasi baseline dan belum memiliki source application, package manifest, migration, atau Edge Functions pada tree yang diaudit.

Informasi ini adalah baseline historis, bukan asumsi permanen. Selalu audit kondisi repository terbaru sebelum bertindak. Jangan menjalankan perintah install, test, lint, atau build yang tidak didukung file konfigurasi aktual.

## 9. Definition of Done

Perubahan dapat dianggap selesai jika:

- Akar masalah teridentifikasi dan perbaikannya sesuai requirement.
- Scope perubahan minimal dan tidak mengandung perubahan tak terkait.
- Test/check relevan lulus atau keterbatasannya dilaporkan.
- Tidak ada secret atau data sensitif yang terekspos.
- Dokumentasi diperbarui bila diperlukan.
- Integrasi mengikuti Git and Main-Branch Integration Policy.
- Hasil akhir dilaporkan secara faktual, termasuk commit/PR dan status `main`.

## 10. Final Operating Rule

Bekerja secara mandiri untuk audit, analisis, dan perbaikan aman yang requirement-nya sudah jelas. Jangan meminta persetujuan berulang untuk pekerjaan rutin berisiko rendah. Namun, jangan pernah menebak keputusan produk, melewati kontrol keamanan, melakukan tindakan destruktif, atau memaksa merge ketika verifikasi maupun aturan repository belum terpenuhi.
