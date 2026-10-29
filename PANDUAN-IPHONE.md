# SIPETAK v3.8.3 — HOTFIX Worker AI dari iPhone (tanpa Terminal)

## Mengapa error muncul

Worker AI v3.8.2 memanggil `fetch()` dengan `redirect: "error"` saat memeriksa token Supabase. Cloudflare Workers **tidak mendukung** redirect mode tersebut; runtime dapat melempar `TypeError` sebelum HTTP request terkirim. Kode lama menangkapnya sebagai `AI_SUPABASE_NETWORK`, sehingga muncul pesan _"Worker tidak dapat menghubungi Supabase"_ walaupun URL dan kredensial pengguna bisa saja benar.

Versi v3.8.3 menggantinya dengan **`redirect: "manual"`** dan **menolak semua 3xx** agar Bearer token tidak diteruskan ke alamat redirect. Tidak ada pemeriksaan login yang dilewati.

## Cara memasang — satu file saja

1. Pastikan Anda menggunakan GitHub repository **`sipetak-asisten-ai`**, **bukan** repository aplikasi `SIPETAK`.
2. Ekstrak file ZIP ini dengan aplikasi **File** pada iPhone. Di dalamnya ada `index.mjs` dan panduan ini.
3. Buka GitHub di Safari, masuk ke repository `sipetak-asisten-ai` pada **branch deploy** yang terhubung ke Cloudflare.
4. Buka file `index.mjs` pada root repository, pilih **Edit** (ikon pensil), kemudian ganti isinya dengan `index.mjs` baru. Jika menggunakan GitHub Upload files, pastikan file **mengganti** file root yang lama, bukan membuat folder baru.
5. **Commit changes**. Periksa Cloudflare Dashboard → **Workers & Pages → sipetak-asisten-ai → Deployments/Builds** hingga deploy terbaru berhasil.
6. Di Safari, buka `https://sipetak-asisten-ai.terlanjurtamvan18.workers.dev/health`. Respons yang diharapkan berisi `"version":"3.8.3"`. Nilai `configured:true` menunjukkan binding tersedia, **tidak** menguji akses jaringan Supabase.
7. Buka SIPETAK melalui ikon iPhone → menu **Supabase** (pastikan login akun yang benar) → **Asisten AI**. Uji kembali dengan materi contoh tidak sensitif dan persetujuan yang sudah diperiksa.

## Jika masih belum berhasil

- `AI_SESSION_INVALID`: login Supabase perlu diperbarui; jangan pakai akun dummy.
- `AI_SUPABASE_REDIRECT`: alamat proyek Supabase salah atau proyek mengalihkan koneksi, periksa Worker `SUPABASE_URL`.
- `AI_SUPABASE_NETWORK` atau `AI_SUPABASE_TIMEOUT`: ada masalah koneksi sungguhan; periksa Supabase Dashboard apakah proyek **Active**, dan periksa variabel Worker.
- `/health` masih `3.8.2`: Worker belum memakai perubahan kode; periksa repo dan deployment terhubung.
- `/health` `configured:false`: periksa binding `AI`, `AI_RATE_LIMIT`, `ALLOWED_ORIGIN` pada Cloudflare.

**Jangan menghapus data/brankas**, mengganti `index.html`, memasukkan kata sandi, atau menaruh secret key Supabase ke GitHub. Pembaruan ini hanya mengubah kode Worker AI, bukan frontend dan bukan database.

Pengujian lokal terhadap alur autentikasi telah dilakukan; **pengiriman nyata ke Cloudflare dan pengujian Safari iPhone Anda tetap harus dilakukan setelah deploy**.

Sumber teknis: https://developers.cloudflare.com/workers/runtime-apis/request/ (bagian redirect).
