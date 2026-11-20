# Portal Kepegawaian RSUD Talaud

GitHub Pages hanya menampilkan halaman peluncur. Aplikasi pegawai/admin, autentikasi, data, dokumen Drive, dan notifikasi berjalan di Google Apps Script milik administrator.

## Pasang ke Apps Script

1. Di editor Apps Script, ganti `Code.gs` dengan file [Code.gs](./Code.gs).
2. Buat file HTML bernama `Portal` (tanpa `.html`) dan salin isi [Portal.html](./Portal.html).
3. Di **Project Settings → Script Properties**, tambahkan:
   - `GOOGLE_OAUTH_CLIENT_ID`: OAuth Client ID tipe **Web application** dari Google Cloud.
   - `GEMINI_API_KEY`: opsional, untuk Asisten AI admin saja. Simpan hanya di Script Properties.
   - `GEMINI_MODEL`: opsional; bawaan `gemini-2.5-flash`.
4. Dari akun Google pemilik penyimpanan, pilih fungsi `setupStorage_` di editor Apps Script lalu klik **Run** dan setujui izin Google. Fungsi ini membuat folder akar, Google Sheet, sheet data, dan rahasia hash password. `DATA_OWNER_EMAIL` dibuat otomatis dan tidak perlu diisi manual.
5. Siapkan OAuth Client ID untuk Google Identity Services dan izinkan origin aplikasi Apps Script sesuai URL deployment. Masukkan Client ID ke Script Properties, simpan, lalu deploy versi baru.
6. Deploy sebagai **Web app**, pilih **Execute as: Me** (akun pemilik data). Web app perlu dapat dibuka untuk menampilkan halaman login pegawai; operasi data selalu diperiksa di server dengan token sesi pegawai atau token Google pemilik. Jangan mengubah pemeriksaan akses server.
7. Tambahkan pegawai dari menu admin. Setiap pegawai mendapat folder Drive dengan nama dan ID, baris akun username, serta folder/file dan notifikasi tersendiri. Password awal minimal 12 karakter; sheet hanya menyimpan hash dan salt.
8. Perbarui `index.html` di repository GitHub Pages dan pastikan URL deployment Apps Script pada tombol **Buka Portal Aman** sesuai dengan deployment terbaru.

## Penyimpanan

Apps Script membuat Google Sheet dengan tab `Employees`, `PersonalData`, `Users`, `Documents`, `Requests`, `Notifications`, dan `AuditLog`. Berkas PDF/JPG/PNG (maksimal 1 MB per file) tersimpan di folder Drive pegawai. Notifikasi dan status baca tersimpan di tab `Notifications`.

Admin masuk dengan Google Identity dan hanya akun yang sama dengan pemilik saat `setupStorage_` dijalankan yang dapat mengelola seluruh data. Pegawai masuk dengan username/password dan hanya dapat membaca profil, dokumen, pengajuan, serta notifikasinya sendiri. Pengajuan perubahan harus disetujui admin. Data pribadi tambahan diterima sebagai JSON pada formulir admin/pegawai.

Data lokal dari prototipe lama **tidak dimigrasikan otomatis**. Masukkan dan verifikasi data melalui aplikasi admin sebelum digunakan. Uji dulu dengan data fiktif, tinjau izin Drive/Sheet dan kebijakan institusi, aktifkan verifikasi dua langkah akun pemilik, serta siapkan backup dan prosedur pemulihan. Jangan bagikan password, token Google, atau API key.
