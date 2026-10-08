# Source lengkap portfolio Afghani Hilmi

Paket ini berisi source aplikasi portfolio yang diterbitkan di `https://afghani-hilmi.ptajitt15.chatgpt.site`, berdasarkan commit yang dicatat pada `SOURCE_MANIFEST.json`. Semua source yang terlacak, lockfile, migrasi database, foto, CV, sertifikat, screenshot, dan video preview bawaan disertakan. Contoh konfigurasi tidak berisi password atau token.

## Isi aplikasi

- Halaman Indonesia / English dan case study per proyek.
- Panel admin: profil, pengalaman, pendidikan, status kerja, proyek, galeri, sertifikat, dan artikel.
- Form kontak dengan kotak masuk admin yang tersimpan.
- Statistik aktivitas nyata dengan kunjungan admin dikecualikan.
- Upload JPG, PNG, WebP, MP4, WebM, dan PDF.
- Schema dan migrasi database, pengujian alur server, serta aset asli bawaan.

## Struktur

| Folder/file | Fungsi |
|---|---|
| `site/runtime.ts` | Routing server, pemeriksaan akses, kontak, statistik, dan media |
| `site/render.ts` | HTML halaman publik dan metadata |
| `site/admin.html` | Halaman panel admin |
| `site/seed.json` | Konten awal portfolio |
| `site/translations.json` | Terjemahan antarmuka |
| `public/` | CSS, JavaScript browser, CV, gambar, sertifikat, dan video |
| `db/schema.ts` | Schema Drizzle untuk SQLite / D1 |
| `drizzle/` | Migrasi database yang telah dibuat |
| `app/[[...path]]/route.ts` | Penghubung Vinext dengan runtime server |
| `build/`, `scripts/`, `lib/`, `components/` | Source pendukung build dan framework |
| `pnpm-lock.yaml` | Versi dependency untuk instalasi yang konsisten |
| `.env.example` | Nama konfigurasi admin; isi dengan nilai sendiri |
| `.openai/hosting.json` | Identitas deployment Sites asal dan nama binding DB / BUCKET |
| `SOURCE_MANIFEST.json` | Commit asal dan checksum semua file asal |

`node_modules`, hasil build, cache, konfigurasi lokal, kredensial, dan riwayat Git tidak disertakan. Dependency dipasang kembali dari lockfile. `.gitignore` berbeda sedikit dari source asal agar contoh `.env.example` boleh disimpan dan `.dev.vars` asli tidak masuk Git.

## Menjalankan dan memeriksa source

Gunakan Node.js 24 atau yang lebih baru dan pnpm versi yang tercantum dalam `package.json` (11.25.0). Jalankan di folder project:

```sh
pnpm install --frozen-lockfile
pnpm exec drizzle-kit generate
node tests/runtime.mjs
pnpm build
```

Migrasi schema bawaan sudah ada; perintah `drizzle-kit generate` diperlukan setelah schema diubah. Pengujian memakai SQLite terisolasi dan media di memori, bukan database live.

Build yang berhasil menghasilkan Worker pada `dist/server/index.js` dan aset pada `dist/client/`. Build bukan berarti database, login, atau domain hosting baru sudah dikonfigurasi.

## Memindahkan dari Sites

### 1. Tentukan jenis hosting

Versi ini adalah aplikasi fullstack Vinext / Cloudflare Workers dengan database D1 dan penyimpanan R2. Cloudflare Workers di akun Anda sendiri paling dekat dengan runtime asal. Untuk VPS/Node, Vercel, atau layanan lain, adapter runtime, database, penyimpanan, dan autentikasi perlu disesuaikan.

Pada shared hosting PHP/cPanel seperti hosting WordPress, mengunggah folder `public/` atau ZIP saja tidak menjalankan form kontak, admin, database, dan artikel dinamis. Memakai hosting tersebut membutuhkan port backend atau layanan backend terpisah.

### 2. Buat database dan penyimpanan sendiri

- Buat database D1 baru dengan binding `DB` dan bucket R2 baru dengan binding `BUCKET` jika memakai Cloudflare.
- Gunakan konfigurasi Wrangler milik akun tujuan. Ganti ID D1 placeholder dan nama bucket pada konfigurasi build; jangan menggunakan sumber daya milik Sites.
- Terapkan file SQL pada `drizzle/` ke database tujuan melalui migrasi Wrangler, sebelum menjalankan aplikasi.
- Pada hosting non-Cloudflare, ganti akses `env.DB` dan `env.BUCKET` di `site/runtime.ts` / `site/db.ts` dengan adapter SQLite/database dan penyimpanan yang sesuai. Route saat ini mengimpor `env` dari `cloudflare:workers`.
- `.openai/hosting.json` adalah konfigurasi asal. Jangan mendaftarkan deployment hosting lain sebagai Site baru menggunakan project ID tersebut. Jika konfigurasi asal dibuang, sesuaikan juga import-nya di `vite.config.ts`.

### 3. Ganti autentikasi admin untuk hosting di luar Sites

Login versi asal memakai `/signin-with-chatgpt` yang disediakan platform Sites, serta header identitas terpercaya `oai-authenticated-user-id` dan `oai-authenticated-user-email`. Route login itu tidak dibuat oleh aplikasi ini dan tidak otomatis tersedia di domain hosting baru.

`ADMIN_EMAIL` mempertahankan admin utama; `ADMIN_EMAILS` berisi tambahan admin yang dipisahkan koma. Nilai asli disimpan sebagai konfigurasi runtime dan tidak disertakan dalam paket. Isi sendiri setelah sistem login baru selesai disambungkan.

Di hosting baru, hubungkan login/session atau identity proxy yang memvalidasi identitas di sisi server, lalu sesuaikan fungsi `admin()` dan route `/admin`. Jangan menganggap header identitas yang dikirim langsung oleh browser sebagai bukti login. Jangan mengganti validasi dengan email yang diketik bebas pada form.

Paket ini mempertahankan autentikasi source asal. Implementasi login baru dan pengujian autentikasi hosting tujuan merupakan pekerjaan migrasi berikutnya; source ini tidak mengklaim login admin langsung berfungsi di luar Sites.

### 4. Ubah domain

Ganti origin lama pada `site/render.ts` agar canonical dan hreflang memakai domain baru. Periksa juga link pada `site/template.html`, `site/seed.json`, dan `public/project-resume.html`. Link demo proyek lain boleh tetap memakai alamat yang sekarang jika demo tersebut masih digunakan.

### 5. Pindahkan data live secara terpisah bila diperlukan

Source dan aset bawaan sudah lengkap. Data yang ditulis setelah deployment tersimpan di D1/R2, bukan di repository: edit CMS, pesan kontak, statistik, dan file upload tambahan tidak otomatis masuk dalam ZIP ini.

Untuk mempertahankan data tersebut, ekspor database dan salin objek media dari lingkungan asal ke tujuan dengan otorisasi pemilik. Pastikan URL dan ID objek sesuai. Jangan memasukkan pesan kontak atau rahasia runtime ke repository publik.

## Memasukkan ke GitHub

Buat repo khusus, misalnya `afghani-hilmi-portfolio`, pada akun `afghanihilmia97`. Repo private dapat dipakai sebagai source hosting; pilih public hanya jika memang ingin seluruh aset, termasuk CV, bisa diunduh siapa saja.

Untuk upload otomatis melalui koneksi GitHub di ChatGPT, repo tujuan harus dapat diakses oleh koneksi tersebut. Inisialisasi repo dengan README agar branch `main` dan commit awal tersedia, lalu berikan link repository. Source dapat dimasukkan dalam satu commit tanpa mengubah repo profil.

Untuk push melalui komputer sendiri:

```sh
git init -b main
git add .
git commit -m "Import complete Afghani Hilmi portfolio source"
git remote add origin https://github.com/afghanihilmia97/afghani-hilmi-portfolio.git
git push -u origin main
```

Perintah tersebut ditujukan ke repo baru yang benar-benar kosong. Jika repo sudah berisi README/commit, clone repo tersebut, salin isi paket ke dalam clone, lalu commit dan push biasa; jangan force-push atau menimpa riwayat yang sudah ada.

## Referensi resmi

- Cloudflare D1: <https://developers.cloudflare.com/d1/get-started/>
- Migrasi D1: <https://developers.cloudflare.com/d1/reference/migrations/>
- Konfigurasi Wrangler: <https://developers.cloudflare.com/workers/wrangler/configuration/>
- Worker dan aset statis: <https://developers.cloudflare.com/workers/static-assets/>

File ini merupakan panduan migrasi. Tidak ada perubahan pada website live dan tidak ada deployment hosting baru yang dilakukan oleh ekspor source.
