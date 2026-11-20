# Portal Kepegawaian PPPK RSUD Talaud

Prototipe frontend statis untuk GitHub Pages. Tidak memerlukan proses build atau instalasi paket.

## Menjalankan

Prototipe tersedia dalam satu file, `index.html`, yang dapat dibuka langsung di browser atau diunggah ke GitHub.

Untuk menerbitkan melalui GitHub Pages:

1. Buka **Settings → Pages** pada repository.
2. Pada **Build and deployment**, pilih **Deploy from a branch**.
3. Pastikan `index.html` berada di root branch; pilih folder **/(root)**, lalu simpan.
4. Buka alamat Pages yang ditampilkan GitHub setelah deployment selesai.

Akun demo:

- Admin: username `admin`, password `admin123`.
- Pegawai: username `pegawai`, password `pegawai123`; ditautkan ke profil demo Maria T. Rondonuwu.

Akun admin dapat mengelola seluruh data. Akun pegawai hanya melihat profil yang ditautkan, tanpa akses ke laporan agregat atau kontrol pengelolaan. Akun dan login hanya simulasi di browser, bukan sistem keamanan untuk penggunaan nyata.

## Fitur prototipe

- Dashboard ringkasan pegawai.
- Tambah, edit, hapus, cari, dan filter data pegawai.
- Impor dan ekspor CSV. Impor memakai header `name,nip,type,position,unit,start,end`; kolom `status,email,phone,id` opsional. Tanggal memakai format `YYYY-MM-DD`.
- Data tersimpan di `localStorage` browser yang sedang dipakai; tombol **Reset data demo** mengembalikan data awal.
- Dashboard personal pegawai dengan indikator kelengkapan profil.
- Biodata, riwayat pendidikan/pekerjaan/pelatihan, data keluarga/anak, dan tanggungan yang dapat dikelola lokal.
- Pengajuan perubahan biodata yang menunggu simulasi persetujuan admin.
- Arsip dokumen PDF/JPG/PNG lokal (maksimal 1 MB total), beserta preview, download, ganti, dan hapus.
- Cetak biodata, DRH, dan preview KP4 melalui dialog cetak browser (dapat dipilih **Save as PDF**).

## Batasan penting

Ini hanya prototipe frontend: login dan persetujuan bukan autentikasi/workflow sungguhan, data hanya ada di browser/perangkat ini, dan berkas disimpan lokal tanpa enkripsi atau private storage. Pegawai demo hanya melihat profil yang ditautkan pada akun demo tersebut. Belum ada koneksi Google. Jangan gunakan NIK, rekening, data keluarga, atau dokumen pegawai nyata. Untuk pemakaian operasional diperlukan backend, autentikasi aman, kontrol akses server-side, penyimpanan terpusat privat, backup, serta peninjauan keamanan dan privasi.
