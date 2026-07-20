# SandiFA — Implementasi DFA untuk Validasi Password Berdasarkan Aturan Keamanan Berbasis Web

Proyek Ujian Akhir Semester (UAS) Mata Kuliah **Teori Bahasa dan Otomata**.
Studi kasus: penerapan **Deterministic Finite Automaton (DFA)** untuk memvalidasi kekuatan kata sandi (password) secara real-time di sisi klien (browser), tanpa mengirim atau menyimpan kata sandi ke server manapun.

🔗 **Aplikasi live:** `<isi dengan URL .my.id setelah deploy>`
🎥 **Video presentasi:** `<isi dengan URL YouTube>`
📄 **Artikel jurnal:** lihat folder `artikel/`

---

## 1. Deskripsi Studi Kasus

Kebijakan kata sandi yang aman umumnya mensyaratkan beberapa kondisi sekaligus, misalnya:

- panjang minimal 8 karakter,
- mengandung minimal satu huruf besar (A–Z),
- mengandung minimal satu huruf kecil (a–z),
- mengandung minimal satu digit (0–9),
- mengandung minimal satu karakter simbol (`!@#$%^&*()_+-=[]{}`).

Setiap kondisi di atas adalah **bahasa reguler**, sehingga masing-masing dapat dikenali oleh sebuah DFA sederhana. Karena kelas bahasa reguler **tertutup terhadap operasi interseksi**, kelima DFA tersebut dapat digabungkan melalui **konstruksi produk (product construction)** menjadi satu DFA tunggal yang menerima kata sandi jika dan hanya jika seluruh syarat terpenuhi secara simultan.

## 2. Jenis Automaton yang Diimplementasikan

DFA produk `M = M1 × M2 × M3 × M4 × M5`, dengan:

| Sub-automaton | Bahasa yang dikenali | Jumlah state |
|---|---|---|
| M1 | `{ w ∈ Σ* : \|w\| ≥ 8 }` (saturating counter 0..8) | 9 |
| M2 | mengandung minimal satu huruf besar | 2 |
| M3 | mengandung minimal satu huruf kecil | 2 |
| M4 | mengandung minimal satu digit | 2 |
| M5 | mengandung minimal satu simbol | 2 |

Total state produk: 9 × 2 × 2 × 2 × 2 = **144** state hidup + 1 state **trap** (untuk karakter di luar alfabet Σ, misalnya spasi) = **145 state**.

Definisi formal lengkap `M = (Q, Σ, δ, q0, F)` beserta diagram transisi tersedia pada artikel jurnal di folder `artikel/` dan pada halaman aplikasi (bagian "Definisi Formal").

```
Q  = {0..8} × {0,1}⁴ ∪ {trap}
Σ  = A-Z ∪ a-z ∪ 0-9 ∪ { ! @ # $ % ^ & * ( ) _ + - = [ ] { } }
q0 = (0,0,0,0,0)
F  = { (l,u,w,d,s) ∈ Q : l = 8 ∧ u = w = d = s = 1 }
```

## 3. Struktur Repository

```
sandifa/
├── index.html      # aplikasi web (self-contained: HTML + CSS + JS)
├── README.md       # dokumen ini
└── artikel/        # artikel jurnal ilmiah (.docx / .pdf) + hasil cek plagiasi
```

## 4. Cara Menjalankan Secara Lokal

Aplikasi ini murni client-side (tidak ada backend/database), sehingga cukup:

```bash
git clone <url-repo-ini>
cd sandifa
# opsi 1: buka langsung
open index.html          # macOS
xdg-open index.html      # Linux
start index.html         # Windows

# opsi 2: jalankan lewat server statis lokal (disarankan agar semua fitur browser aktif normal)
python3 -m http.server 8000
# lalu buka http://localhost:8000
```

## 5. Cara Deploy ke Domain `.my.id`

1. Daftarkan/aktifkan domain gratis `namaanda.my.id` melalui penyedia seperti Domainesia atau PANDI.
2. Deploy `index.html` sebagai situs statis, misalnya melalui **Cloudflare Pages**, **Netlify**, **Vercel**, atau **GitHub Pages**.
3. Arahkan (point) domain `.my.id` ke deployment tersebut melalui CNAME/A record sesuai dokumentasi penyedia hosting.
4. Pastikan aplikasi dapat diakses tanpa autentikasi, lalu uji di mode incognito.

## 6. Cara Kerja Automaton (Ringkas)

Setiap karakter yang diketik pada input password diproses satu per satu:

1. Karakter diklasifikasikan (`A-Z`, `a-z`, `0-9`, simbol, atau di luar Σ).
2. Jika di luar Σ (misalnya spasi) → state berpindah ke `trap` dan tidak bisa pulih (irreversible reject), sesuai sifat DFA yang deterministik dan tidak mengingat histori di luar representasi state-nya.
3. Jika valid → flag sub-automaton terkait diset menjadi 1, dan counter panjang bertambah (dibatasi maksimum 8).
4. Setelah seluruh karakter diproses, kata sandi **diterima** jika dan hanya jika state akhir berada di himpunan `F`.

## 7. Teknologi

- HTML5, CSS3 (custom properties, grid layout), JavaScript murni (vanilla, tanpa framework/library eksternal)
- SVG untuk visualisasi diagram automaton secara live
- Tidak ada dependensi build tool — cukup satu berkas `index.html`

## 8. Lisensi & Atribusi

Dibuat untuk keperluan akademik (UAS Teori Bahasa dan Otomata). Kode sumber dapat digunakan kembali untuk keperluan pembelajaran.

## 9. Riwayat Pengembangan

Lihat riwayat commit pada repository ini untuk tahapan pengembangan (perancangan automaton → implementasi simulator → penyusunan UI/diagram → pengujian → dokumentasi).
