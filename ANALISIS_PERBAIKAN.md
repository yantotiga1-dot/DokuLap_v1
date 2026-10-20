# DokuLap — Analisis dan Perbaikan

## Ringkasan

Paket merupakan frontend statis HTML/CSS/JavaScript dengan backend Google Apps Script, Google Sheets, dan Google Drive. GitHub Pages hanya menyajikan frontend; backend Apps Script dan otorisasi Google tetap harus dikonfigurasi secara terpisah.

## Perbaikan yang diterapkan

1. **Konfigurasi backend lebih mudah** — URL Web App dapat diatur dari halaman login, disimpan per-browser, dan diuji melalui aksi `health`.
2. **Validasi upload sisi klien** — file di atas 8 MiB ditolak sebelum dikonversi ke Base64; error pembacaan file dibuat lebih jelas. Backend tetap melakukan validasi ukuran.
3. **Dokumentasi URL diperbarui** — instruksi lama yang merujuk variabel API yang tidak ada telah diselaraskan dengan implementasi.
4. **GitHub Pages workflow** — ditambahkan workflow deploy otomatis untuk branch `main` serta `.nojekyll`. Pilih GitHub Actions di Settings → Pages.
5. **Dokumentasi yang dihapus tidak muncul lagi** — backend tetap menyimpan baris untuk audit, tetapi endpoint daftar dokumentasi mengecualikan status `Dihapus`, sehingga item tidak muncul kembali dan tidak ikut dihitung pada analisis.
6. **Satu kegiatan aktif pada satu waktu** — backend menolak pembuatan kegiatan baru saat masih ada kegiatan berstatus Aktif, agar konteks upload tidak salah sasaran; halaman Kegiatan menyinkronkan ID aktif dari data server.
7. **Refresh setelah upload dari halaman Dokumentasi** — daftar dimuat ulang setelah semua file berhasil diunggah agar tampilan konsisten dengan backend.

## Hasil pemeriksaan statis

- `assets/js/api.js`: lolos `node --check`.
- `assets/js/app.js`: lolos `node --check`.
- `assets/js/pages.js`: lolos `node --check`.
- JavaScript inline di `index.html`: lolos `node --check`.
- Backend `Code.gs` ditinjau secara statis; runtime Google Apps Script tidak tersedia dalam pemeriksaan lokal ini.

## Syarat sebelum produksi

- Jalankan `setupDokuLap()` dari Apps Script yang terikat pada Spreadsheet database yang benar dan berikan izin Google yang diperlukan.
- Deploy sebagai Web App (`Execute as: Me`, akses `Anyone` sesuai kebutuhan akses aplikasi) dan gunakan URL `/exec` terbaru.
- Ganti password awal `admin` / `DokuLap@12345` sebelum aplikasi dipublikasikan. Jangan menggunakan akun awal di produksi.
- Pastikan Apps Script benar-benar dapat dipanggil dari origin GitHub Pages di browser yang akan digunakan. Tombol **Tes koneksi** membantu menguji hal ini; jika browser melaporkan CORS atau redirect error, masalah tersebut harus diselesaikan pada pola integrasi backend, bukan dianggap berhasil.
- Uji alur nyata: login, tambah sekolah, buat kegiatan, upload file kecil, buka preview/Drive, hapus dokumentasi, selesaikan kegiatan, dan pastikan data tetap benar setelah refresh.

## Batasan yang belum bisa dinyatakan lulus

Tidak ada akses ke Spreadsheet, Drive, deployment Apps Script, atau akun GitHub pemilik dari pemeriksaan paket ini. Karena itu, fungsi integrasi langsung dan deployment tidak dapat diklaim sudah lulus end-to-end hanya dari pemeriksaan kode. Upload dibatasi 8 MiB per file dan tetap memakai Base64; video besar memerlukan rancangan upload berbeda. Modul Laporan masih placeholder, bukan generator laporan.
