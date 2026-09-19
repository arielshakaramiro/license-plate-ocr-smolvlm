# License Plate OCR dengan SmolVLM

Pendekatan modern untuk text recognition: alih-alih pipeline CRNN + CTC klasik, repo ini pakai vision-language model (VLM) — `HuggingFaceTB/SmolVLM-Instruct` — yang di-prompt dengan bahasa natural untuk mengekstrak teks dari gambar dan mengembalikannya sebagai JSON terstruktur.

## Gambaran Umum

Text recognition nggak selalu butuh melatih model khusus. VLM serbaguna bisa langsung di-prompt untuk membaca teks dari gambar, dengan format ekstraksi dikontrol sepenuhnya lewat prompt (di sini: teks polos saja, tanpa karakter tambahan, dikembalikan sebagai JSON).

Repo ini mendemonstrasikan pendekatan itu pada gambar plat nomor:

1. Load gambar
2. Susun prompt gaya chat yang menginstruksikan model berperan sebagai license plate extractor dan mengembalikan JSON
3. Jalankan prompt + gambar lewat VLM
4. Decode teks yang dihasilkan

## Model

`HuggingFaceTB/SmolVLM-Instruct` — vision-language model yang ringkas, dimuat lewat `transformers` (`AutoProcessor` + `AutoModelForImageTextToText`), dijalankan dalam `bfloat16` di GPU kalau tersedia.

## Hasil (terverifikasi — hasil run Colab asli)

Input: foto close-up plat nomor depan mobil, diupload manual dan ditampilkan langsung di notebook sebagai referensi.

![Foto plat nomor input](images/plat-mobil.jpeg)

Prompt (cuplikan): *"You are an expert license plate extractor agent. Extract this text with only the text, no additional characters... Return it in JSON format."*

Raw output model (cuma token baru yang di-decode, prompt-nya nggak ikut ke-echo):
```json
{
    "text": "B 4213 L"
}
```

Output ini kemudian di-parse beneran pakai `json.loads()`, bukan cuma di-print apa adanya — notebook-nya kasih tau jelas kalau parsing-nya gagal, bukan asumsi outputnya selalu JSON valid. Di run ini, parsing-nya berhasil dan teks plat yang diekstrak cocok persis dengan plat yang terlihat di gambar sumber.

> **Catatan cakupan:** ini satu contoh kualitatif, bukan benchmark. Keandalan OCR sebuah VLM bervariasi tergantung sudut gambar, pencahayaan, gaya plat, dan font — tidak ada metrik akurasi/error rate lintas banyak sampel yang diukur di sini.

## Catatan setup

- **HF_TOKEN (opsional):** notebook ini pertama-tama cek Colab Secrets untuk `HF_TOKEN`; kalau belum diset, akan diminta input manual. Token nggak wajib untuk model publik ini, tapi menaikkan rate limit API Hugging Face kamu.
- **Input gambar:** notebook ini pakai widget upload file Colab (`google.colab.files.upload()`) daripada fetch gambar dari URL — beberapa host eksternal nge-throttle atau block request dari IP datacenter (seperti yang terjadi saat notebook ini pertama kali coba load gambar demo langsung dari sebuah website), jadi upload lokal jalur yang lebih reliable.

## Cara menjalankan

1. Buka `notebooks/license_plate_ocr_smolvlm.ipynb` di Google Colab
2. Set runtime ke GPU (disarankan; CPU tetap jalan tapi lebih lambat)
3. Jalankan semua cell — saat diminta, upload gambar plat nomor

## Struktur repo

```
.
├── notebooks/
│   └── license_plate_ocr_smolvlm.ipynb
├── images/
│   └── plat-mobil.jpeg
├── LICENSE
├── README.md
└── README.id.md
```

## Lisensi

MIT — lihat [LICENSE](LICENSE).
