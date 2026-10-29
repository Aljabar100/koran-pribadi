# Koran Pribadi

Koran pribadi harian: **satu halaman statis** yang menampilkan edisi hari ini —
rangkuman berita terpanas + bagian hoax/cek fakta. Dibuat untuk dibaca pemilik
sendiri, bukan untuk publik. Tidak ada backend, database, atau login.

## Cara membuka website

1. Buka file `index.html` di browser (klik dua kali, atau seret ke jendela browser).
   - Tidak perlu server. Halaman tampil offline; internet hanya dibutuhkan saat
     men klik tautan sumber.
   - Bila ingin lewat server lokal: jalankan `python3 -m http.server` di folder
     ini, lalu buka `http://localhost:8000/`.
2. Halaman membaca data dari `edisi-terkini.js`. Selama file itu ada, edisi langsung tampil.

## Isi proyek

- `index.html` — halaman, tampilan (CSS), dan logika render. **Tidak perlu diedit** saat ganti edisi.
- `edisi-terkini.js` — **DATA edisi hari ini** (objek `window.EDISI`). File inilah yang diganti tiap edisi baru.
- `edisi/2026-10-29.js` — arsip salinan edisi 29 Okt 2026 (contoh format arsip).
- `README.md` — file ini.

## Struktur data (edisi-terkini.js)

```js
window.EDISI = {
  tanggal: "Kamis, 29 Oktober 2026",
  bagian1: [ { judul, ringkasan, penting, sumber, url, tanggal } ], // 10–12 berita
  bagian2: [ { klaim, label, alasan, sumber, url, tanggal } ]       // 3–5 cek fakta
};
```

- `label` di bagian2 hanya boleh salah satu: `HOAX`, `MENYESATKAN`, atau `BELUM TERBUKTI`.

## Cara membuat edisi hari berikutnya

Langkah **persis** yang dipakai untuk edisi 29 Okt 2026 (dikerjakan manual dengan
bantuan agen AI + verifikasi tiap sumber):

1. **Tentukan tanggal edisi** (tanggal hari kerja, zona WIB).
2. **Kumpulkan kandidat berita panas** dari halaman depan media kredibel:
   - Indonesia: ANTARA (`antaranews.com/berita/terkini`), Kompas (`kompas.com` & `kompas.id`), Detik, Tempo.
   - Internasional: Reuters, BBC, AP, Al Jazeera, CNA.
   - Catat judul + URL + tanggal artikel untuk tiap kandidat.
3. **Pilih 10–12 berita terpanas** — peristiwa yang diliput banyak media, berdampak
   besar, atau berkembang cepat. Politik/ekonomi/teknologi/bencana/perang campur
   dalam satu daftar (bukan per kategori).
4. **Gabungkan peristiwa yang sama** dari banyak media menjadi SATU item (jangan
   tampilkan dobel). Pilih satu URL sumber utama yang benar-benar mendukung rangkuman.
5. **Baca/saring isi tiap artikel** (buka link atau ekstraksi halaman). Tulis per item:
   - Judul rangkuman sendiri yang jelas (bukan clickbait).
   - Ringkuman 3–5 kalimat, bahasa Indonesia lugas. **Angka, nama, tanggal, kutipan
     HARUS sesuai sumber.** Kalau ragu, tulis lebih umum — jangan mengarang detail.
   - Satu kalimat "kenapa penting".
   - Tautan ke sumber asli.
6. **Kumpulkan 3–5 item cek fakta** dari: Tempo CekFakta, Mafindo/TurnBackHoax
   (`turnbackhoax.id`), CekFakta ANTARA, Liputan6 Cek Fakta, atau Kominfo. Pilih yang
   bertanggal hari/minggu berjalan.
7. **Tentukan label dari hasil cek fakta sumber — jangan tebak:**
   - dinyatakan palsu/penipuan → `HOAX`;
   - dinyatakan keliru/tidak utuh/menyesatkan → `MENYESATKAN`;
   - belum ada cek fakta atau sumber primer → `BELUM TERBUKTI` (jangan beri label
     HOAX/VALID hanya dari tebakan).
8. **Arsipkan edisi lama**: salin `edisi-terkini.js` ke `edisi/YYYY-MM-DD.js`.
9. **Tulis edisi baru**: ganti seluruh isi `edisi-terkini.js` dengan objek
   `window.EDISI` baru (tanggal baru + `bagian1` + `bagian2`).
10. **Buka `index.html`** dan periksa: tanggal kelihatan, 10–12 berita terbaca,
    bagian hoax terpisah jelas, semua tautan bisa diklik. Cek juga di lebar HP.

### Sumber nyata yang dipakai edisi 29 Okt 2026

- **Berita:** Kompas.id & Kompas.com (demo Sumpah Pemuda, karhutla, KPK–PT Djarum/Bea
  Cukai Kudus, gempa Tanimbar), ANTARA (DEN subsidi listrik, Sekolah Rakyat/MBG),
  BBC (tabrakan kereta Spanyol, pemilu Israel, Flávio Bolsonaro, Microsoft, krisis
  Selat Hormuz), Reuters (Rusia–Ukraina).
- **Cek fakta:** Tempo CekFakta (klaim pertemuan Dino Patti Djalal/Saiful Mujani
  dengan Dubes Singapura; video AI Prabowo saat antre BBM; klaim bantuan dana umat
  Katolik via WhatsApp), TurnBackHoax/Mafindo (bantuan internet gratis Rp250.000).

## Batas jujur — apakah pembuatan edisi bisa jalan otomatis terjadiri?

**Tidak. Edisi ini dibuat manual, belum otomatis.** Jangan diklaim jalan otomatis.

Alasannya:

- Memilih "berita terpanas", menggabungkan peristiwa yang sama dari banyak media,
  dan menulis rangkuman 3–5 kalimat yang akurat (angka/nama/kutipan sesuai sumber)
  butuh pertimbangan dan verifikasi yang **berisiko halusinasi** bila dilakukan
  murni otomatis tanpa peninjauan.
- Label `HOAX`/`MENYESATKAN` **wajib** mengikuti hasil cek fakta sumber — tidak
  boleh ditebak model. Ini butuh pembacaan hasil cek fakta yang nyata.
- Karena itu tiap edisi harus **disiapkan dan ditinjau manual** setiap hari.

Syarat kalau ke depan ingin (sebagian) otomatis:

- Akses terjadwal ke sumber: RSS/API media + lembaga cek fakta, plus alat ringkasan.
- **Output ringkasan + label tetap harus ditinjau** sebelum ditulis ke
  `edisi-terkini.js`, demi aturan kebenaran di atas.
- Tidak ada layanan berbayar atau API key berbayar yang dipakai di proyek ini.
  Otomatisasi penuh **belum dibuktikan jalan**.

## Catatan

- Tidak ada backend, database, atau login.
- Tidak di-deploy ke alamat publik.
- `index.html` memakai `<meta name="robots" content="noindex,nofollow">` agar tidak diindeks mesin pencari.
- Selalu klik tautan sumber untuk membaca berita aslinya — halaman ini hanya rangkuman.

<!-- tes push 29 Okt 2026: verifikasi izin permanen, tanpa perubahan isi situs -->
