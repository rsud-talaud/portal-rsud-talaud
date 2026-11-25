# Portal Kepegawaian RSUD Talaud

GitHub Pages menyajikan antarmuka di `index.html`. API autentikasi dan data berjalan di Cloud Run. Google Sheets dan Drive tetap berada di akun pemilik, sedangkan Cloud Run mengaksesnya melalui service account yang diberi izin terbatas. Apps Script menyiapkan folder dan spreadsheet awal serta menempatkan spreadsheet dan folder pegawai portal di dalam folder penyimpanan RSUD.

## Konfigurasi Google Sheets dan Drive

1. Pastikan akun pemilik Apps Script memiliki akses **Editor** ke [folder penyimpanan RSUD](https://drive.google.com/drive/folders/1n-xE_L6H9QGMl2yPCZKH1cur89VSxiLq).
2. Buka project Apps Script milik akun penyimpan `rsudcloud01@gmail.com` dan salin [Code.gs](./Code.gs) ke editor Apps Script.
3. Jalankan `setupStorage_` dari editor dan setujui izin Sheets/Drive. Script memakai folder yang ditentukan di `CONFIGURED_ROOT_FOLDER_ID`, menaruh spreadsheet di folder tersebut, dan memindahkan folder pegawai portal yang sudah ada ke dalamnya. Buka **Execution log** untuk mendapatkan `spreadsheetId` dan `rootFolderId`.
4. Di Google Cloud, buat service account untuk Cloud Run. Bagikan folder penyimpanan itu dan spreadsheet kepada email service account sebagai **Editor**. Jangan pilih akses publik.

Jika portal sudah berjalan, setelah memperbarui `Code.gs` jalankan kembali `setupStorage_` untuk menambahkan kolom autentikator ke sheet `Users` sebelum memperbarui API.

## Deploy API Cloud Run

Di Google Cloud, aktifkan Cloud Run, Cloud Build, Sheets API, dan Drive API. Buat OAuth Client ID tipe **Web application**, tambahkan origin `https://rsud-talaud.github.io` ke **Authorized JavaScript origins**, lalu selesaikan branding/consent screen Google. OAuth Client ID tersebut dipakai oleh login admin.

Buat secret di Secret Manager:

- `portal-session-secret`: secret acak minimal 32 byte untuk menandatangani sesi pegawai.
- `gemini-api-key`: opsional, untuk Asisten Gemini admin. Jangan taruh key ini di GitHub.

Berikan service account Cloud Run akses membaca secret yang digunakan. Dari folder `backend`, deploy dengan nilai milik Anda:

```powershell
gcloud run deploy rsud-portal-api `
  --source . `
  --region asia-southeast1 `
  --service-account SERVICE_ACCOUNT_EMAIL `
  --allow-unauthenticated `
  --concurrency 1 `
  --max 1 `
  --set-env-vars "OWNER_EMAIL=rsudcloud01@gmail.com,GOOGLE_OAUTH_CLIENT_ID=WEB_CLIENT_ID,SPREADSHEET_ID=SHEET_ID,DRIVE_ROOT_FOLDER_ID=1n-xE_L6H9QGMl2yPCZKH1cur89VSxiLq,CORS_ORIGINS=https://rsud-talaud.github.io" `
  --set-secrets "SESSION_SECRET=portal-session-secret:latest,GEMINI_API_KEY=gemini-api-key:latest"
```

Jika Gemini tidak digunakan, hapus pasangan `GEMINI_API_KEY=...` dari `--set-secrets`. Ganti semua nilai contoh dengan nilai project Anda. `--allow-unauthenticated` hanya membuat halaman login dapat diakses; endpoint data tetap memerlukan sesi pegawai atau token Google pemilik yang diperiksa API. Pertahankan pemeriksaan tersebut dan batasi service ke satu instance/concurrency satu karena login memakai penghitung percobaan di sheet.

## Terbitkan antarmuka GitHub Pages

1. Ambil URL layanan `.run.app` setelah deployment selesai.
2. Ganti `API_BASE_URL` di [index.html](./index.html) dengan URL tersebut, lalu unggah/perbarui file di repository GitHub Pages.
3. Buka `https://rsud-talaud.github.io/portal-rsud-talaud/`, login sebagai admin menggunakan akun Google pemilik, lalu buat akun pegawai.

## Akses dan penyimpanan

- Admin hanya dapat mengelola portal dengan akun Google yang cocok persis dengan `OWNER_EMAIL`.
- Pegawai masuk dengan username/password dan kode TOTP dari Google Authenticator atau Apple Passwords. Pendaftaran awal harus diverifikasi dengan kode lalu disetujui admin; admin dapat mencabut autentikator jika perangkat hilang. Akun tanpa autentikator aktif tidak dapat login atau memakai sesi lama. Password minimal 12 karakter dan disimpan sebagai hash PBKDF2, bukan teks biasa. Pegawai hanya dapat membaca profil, dokumen, pengajuan, dan notifikasi miliknya sendiri.
- Admin harus memverifikasi identitas pegawai dan pendaftaran autentikator melalui prosedur RSUD sebelum menyetujui. TOTP tidak mengikat kode secara kriptografis ke satu perangkat: secret dapat disalin atau disinkronkan, sehingga persetujuan aplikasi saja bukan bukti identitas perangkat fisik.
- Setiap pegawai mendapat folder Drive sendiri. PDF/JPG/PNG dibatasi 1 MB per file.
- Spreadsheet berisi data pegawai, akun, profil, pengajuan, notifikasi, dan audit diletakkan di folder penyimpanan RSUD. Dokumen yang diunggah masuk ke subfolder pegawai bernama `Nama (ID pegawai)`; ID menjaga dua pegawai dengan nama sama tetap terpisah.
- Sheet mencakup `Employees`, `PersonalData`, `Users`, `Documents`, `Requests`, `Notifications`, dan `AuditLog`.
- Data lokal dari prototipe lama tidak dimigrasikan otomatis. Masukkan dan verifikasi data melalui admin sebelum dipakai.

Uji alur end-to-end dengan data fiktif dahulu. Aktifkan verifikasi dua langkah akun Google pemilik, tinjau kebijakan institusi sebelum menyimpan data pribadi, dan siapkan backup. Jangan bagikan password, token Google, secret autentikator, session secret, atau API key.
