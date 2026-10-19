# HyLooks CMS API (Cloudflare Worker)

Worker ini menjadi perantara aman antara halaman CMS di GitHub Pages dan GitHub repository. **Jangan pernah memasukkan `GITHUB_TOKEN` ke `assets/js/*.js`.**

## 1. Buat token GitHub

Buat fine-grained personal access token dengan akses hanya ke repository HyLooks yang dipakai. Beri izin **Contents: Read and write**. Simpan token sebagai Secret di Cloudflare Worker, bukan di file project atau GitHub Pages.

## 2. Deploy Worker

Buat Worker baru di Cloudflare Dashboard, lalu tempel isi `worker.js` sebagai kode Worker dan deploy.

Tambahkan Variables/Secrets pada Worker:

- `GITHUB_TOKEN` — **Secret**, token GitHub di atas.
- `CMS_PASSWORD` — **Secret**, password khusus untuk operasi CMS. Masukkan password yang sama pada kolom CMS Password di halaman admin.
- `GITHUB_OWNER` — username atau organisasi pemilik repository.
- `GITHUB_REPO` — nama repository, misalnya `heylooke`.
- `GITHUB_BRANCH` — branch yang dipakai GitHub Pages, biasanya `main`.
- `ALLOWED_ORIGIN` — origin website CMS, misalnya `https://USERNAME.github.io`. Jika memakai custom domain, isi origin custom domain tersebut. Untuk beberapa origin, pisahkan dengan koma.

`ALLOWED_ORIGIN` harus cocok persis dengan origin browser (protokol + domain, tanpa path). Jangan memakai password yang sama dengan akun GitHub. CORS bukan autentikasi; `CMS_PASSWORD` diperlukan untuk melindungi operasi tulis.

## 3. Hubungkan CMS ke Worker

Edit `assets/js/cms-config.js`, ganti `https://YOUR-WORKER.workers.dev` dengan URL Worker yang sudah di-deploy. Commit dan push perubahan ke GitHub.

## 4. Uji

Buka `admin.html` dari website GitHub Pages, lalu tambahkan satu video percobaan. Setelah sukses, cek commit terbaru di repository: harus memperbarui `assets/data/videos.json` dan menambahkan `assets/thumb/videoNNN.webp` dalam satu commit.

## Catatan

- Token dengan izin Contents write memberi Worker kemampuan mengubah file di repository yang dipilih. Batasi token ke satu repository dan cabut jika diduga bocor.
- Browser menolak origin yang tidak tercantum di `ALLOWED_ORIGIN`.
- GitHub Pages perlu waktu untuk menerbitkan perubahan; refresh dengan cache-busting jika thumbnail atau JSON belum langsung terlihat.
- Input URL video tetap harus berupa URL HTTPS langsung yang berakhir `.mp4`. Encoding hanya menyamarkan URL; tidak meng-host video dan tidak mengatasi hotlink protection/CORS dari penyedia video.
