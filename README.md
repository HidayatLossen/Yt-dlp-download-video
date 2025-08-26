![Windows](https://img.shields.io/badge/OS-Windows-blue?logo=windows)
![Linux](https://img.shields.io/badge/OS-Linux-orange?logo=linux)
![macOS](https://img.shields.io/badge/OS-macOS-lightgrey?logo=apple)
![Python](https://img.shields.io/badge/Python-3.x-green?logo=python)
![yt-dlp](https://img.shields.io/badge/yt--dlp-latest-yellow)
![FFmpeg](https://img.shields.io/badge/FFmpeg-required-success?logo=ffmpeg)

---

# 📑 Daftar Isi

- [Persiapan](#-persiapan)
- [Download Playlist atau Satuan Video](#-download-playlist-atau-satuan-video)
- [Mengetahui Jumlah Video di Playlist](#-mengetahui-jumlah-video-di-playlist)
- [Generate Daftar File (list.txt)](#-generate-daftar-file-listtxt)
- [Membuat Timestamp Otomatis](#-membuat-timestamp-otomatis-dengan-powershell)
- [Rename File Otomatis](#-rename-file-otomatis-powershell)
- [Gabungkan Video dengan FFmpeg](#-gabungkan-video-dengan-ffmpeg)
- [📌 Catatan](#-catatan)

---

# 📥 Panduan Download & Pengolahan Video dengan `yt-dlp` + `ffmpeg` Di Windows

Dokumentasi ini berisi contoh penggunaan **yt-dlp** dan **ffmpeg** untuk mengunduh playlist YouTube, mengelola file video, serta menggabungkannya menjadi satu file final.

---

Oke, saya satukan jadi **README.md** versi final dengan bagian instalasi Python + pip, lalu lanjut ke instalasi yt-dlp dan ffmpeg. Saya buat agar lebih rapi:

---

## 🔧 Persiapan

1. **Instal Python & pip** 

   * Download [Python](https://www.python.org/downloads/) dan lakukan instalasi.
   * Saat instalasi pastikan opsi **"Add Python to PATH"** dicentang.
   * Setelah instalasi, cek apakah Python dan pip sudah aktif:

     ```powershell
     python --version
     pip --version
     ```

2. **Instal yt-dlp**

   ```powershell
   pip install yt-dlp
   ```

   atau download binary dari [yt-dlp releases](https://github.com/yt-dlp/yt-dlp/releases).

3. **Instal ffmpeg**

   * Unduh dari [ffmpeg.org](https://ffmpeg.org/download.html).
   * Ekstrak dan tambahkan folder `bin` ke **PATH**.
   * Verifikasi instalasi:

     ```powershell
     ffmpeg -version
     ```

4. **(Opsional) Cookies YouTube**
   Untuk playlist privat / usia tertentu:

   * Export cookies dari browser ke file (`coba.txt`)
   * Atau gunakan opsi:

     ```powershell
     yt-dlp --cookies-from-browser edge ...
     ```

---

## 🎥 Download Playlist atau Satuan Video

_Silakan baca penjelasan di bawah dan **pilih salah satu command sesuai kebutuhan Anda**. Setiap opsi memiliki fungsi berbeda, jadi gunakan yang paling sesuai dengan kondisi playlist/video yang ingin diunduh._

<p align="center" id="daftar-browser"><b>Daftar Browser</b></p> 
Silahkan di ganti sesuai Browser anda  

| Browser            | Contoh Command                           |
| ------------------ | ---------------------------------------- |
| **Edge**           | `yt-dlp --cookies-from-browser edge`     |
| **Chrome**         | `yt-dlp --cookies-from-browser chrome`   |
| **Chromium**       | `yt-dlp --cookies-from-browser chromium` |
| **Brave**          | `yt-dlp --cookies-from-browser brave`    |
| **Opera**          | `yt-dlp --cookies-from-browser opera`    |
| **Vivaldi**        | `yt-dlp --cookies-from-browser vivaldi`  |
| **Firefox**        | `yt-dlp --cookies-from-browser firefox`  |
| **Safari (macOS)** | `yt-dlp --cookies-from-browser safari`   |


### 1. Download standar dan Skip codec `av01`

Mengunduh video/playlist dengan format terbaik (video MP4 + audio M4A) dan Melewati codec `av01` (karena kadang tidak kompatibel di beberapa perangkat).

```powershell
yt-dlp -f "bv*[ext=mp4][vcodec!*=av01]+ba[ext=m4a]/b[ext=mp4]" --merge-output-format mp4 -o "%(playlist_index)s - %(title)s.%(ext)s" "URL_PLAYLIST atau URL_VIDEO"
```

### 2. Dengan cookies [browser](#daftar-browser) (contoh Edge. pilih sesuai browser yang di tabel).

Memakai cookies dari browser agar bisa mengunduh video private / age restricted.

```powershell
yt-dlp --cookies-from-browser edge -f "bv*[ext=mp4][vcodec!*=av01]+ba[ext=m4a]" --merge-output-format mp4 -o "%(playlist_index)s - %(title)s.%(ext)s" "URL_PLAYLIST atau URL_VIDEO"
```

### 3. Dengan file cookies manual

Menggunakan file cookies (`coba.txt`) yang diexport dari browser

```powershell
yt-dlp --cookies coba.txt -f "bv*[ext=mp4][vcodec!*=av01]+ba[ext=m4a]" --merge-output-format mp4 -o "%(playlist_index)s - %(title)s.%(ext)s" "URL_PLAYLIST atau URL_VIDEO"
```

### 4. Download rentang index tertentu (misal 13–42)

Mengunduh hanya video dengan urutan tertentu dari playlist.

```powershell
yt-dlp --cookies coba.txt -f "bv*[ext=mp4][vcodec!*=av01]+ba[ext=m4a]" --merge-output-format mp4 --playlist-start 13 --playlist-end 42 -o "%(playlist_index)s - %(title)s.%(ext)s" "URL_PLAYLIST"
```

### 5. Download berdasarkan index pilihan

Mengunduh video tertentu sesuai nomor urut di playlist (misalnya 1, 5, 7, 10).

```powershell
yt-dlp --cookies coba.txt -f "bv*[ext=mp4][vcodec!*=av01]+ba[ext=m4a]" --merge-output-format mp4 --playlist-items "1,5,7,10" -o "%(playlist_index)s - %(title)s.%(ext)s" "URL_PLAYLIST"
```

---


## 🔎 Mengetahui Jumlah Video di Playlist

Menampilkan metadata playlist dalam format JSON (berguna untuk tahu jumlah video).

```powershell
yt-dlp --cookies coba.txt -J "URL_PLAYLIST"
```

---


## 📂 Generate Daftar File (`list.txt`)

Kode berikut digunakan untuk membuat daftar file video (`.mp4`) secara otomatis, lalu menyimpannya ke dalam file teks bernama `list.txt`.

```powershell
Get-ChildItem *.mp4 | Sort-Object Name | ForEach-Object { "file '$($_.Name)'" } | Out-File -Encoding ascii list.txt
```

### 🔎 Penjelasan Baris per Baris:

1. **`Get-ChildItem *.mp4`**

   * Mengambil semua file dengan ekstensi `.mp4` di folder saat ini.
   * Sama seperti perintah `dir *.mp4`.

2. **`| Sort-Object Name`**

   * Mengurutkan file berdasarkan **nama** (A → Z).
   * Supaya daftar file rapi sesuai urutan abjad.

3. **`| ForEach-Object { "file '$($_.Name)'" }`**

   * Untuk setiap file, hasilnya diubah ke format:

     ```
     file 'namafile.mp4'
     ```
   * Format ini digunakan oleh **FFmpeg** agar bisa membaca daftar file dari `list.txt`.

4. **`| Out-File -Encoding ascii list.txt`**

   * Menyimpan hasil akhir ke file teks bernama `list.txt`.
   * `-Encoding ascii` digunakan supaya FFmpeg bisa membaca file dengan benar (UTF-8 kadang bermasalah).

---

### 📌 Contoh Hasil `list.txt`

Jika ada file:

```
A.mp4
B.mp4
C.mp4
```

Maka isi `list.txt`:

```
file 'A.mp4'
file 'B.mp4'
file 'C.mp4'
```

File ini bisa langsung dipakai untuk **gabungkan video** dengan FFmpeg:

```powershell
ffmpeg -f concat -safe 0 -i list.txt -c copy output.mp4
```

---

## 🕒 Membuat Timestamp Otomatis dengan PowerShell

Script ini:

* Menghitung durasi setiap video dengan `ffprobe`
* Menghasilkan `timestamp.txt` berisi waktu mulai + judul video

```powershell
# Ambil semua file .mp4, urut berdasarkan nama
$videos = Get-ChildItem *.mp4 | Sort-Object Name
$durations = @()

# Cek ffprobe
if (-not (Get-Command ffprobe -ErrorAction SilentlyContinue)) {
    Write-Error "ffprobe tidak ditemukan. Instal ffmpeg dan tambahkan ke PATH."
    exit
}

foreach ($video in $videos) {
    $duration = & ffprobe -v error -show_entries format=duration -of default=noprint_wrappers=1:nokey=1 "$($video.FullName)"
    $durations += [math]::Round([double]$duration, 2)
}

function Convert-ToTimeCode ($seconds) {
    $ts = [TimeSpan]::FromSeconds($seconds)
    return $ts.ToString("hh\:mm\:ss")
}

$total = 0
$output = @()

for ($i = 0; $i -lt $videos.Count; $i++) {
    $judul = $videos[$i].BaseName -replace "^\d+\s*-\s*", ""
    $timestamp = Convert-ToTimeCode $total
    $output += "$timestamp - $judul"
    $total += $durations[$i]
}

$output | Out-File -Encoding UTF8 timestamp.txt
```

---

## ✏️ Rename File Otomatis (PowerShell)

```powershell
# Input judul umum
$judul = Read-Host "Masukkan judul untuk semua video"

$files = Get-ChildItem *.mp4 | Sort-Object Name
for ($i=0; $i -lt $files.Count; $i++) {
    $number = "{0:D2}" -f ($i+1)
    $newName = "$number - $judul$($files[$i].Extension)"
    Rename-Item $files[$i].FullName $newName
}
```

---

## 🔗 Gabungkan Video dengan FFmpeg

### 1. Concat langsung (tanpa re-encode)

```powershell
ffmpeg -f concat -safe 0 -i list.txt -c copy gabungan.mp4
```

### 2. Concat dengan re-encode (jika error)

```powershell
ffmpeg -f concat -safe 0 -i list.txt -c:v libx264 -preset veryfast -crf 22 -c:a aac -b:a 192k gabungan_final.mp4
```

### 3. Perbaikan file error individual

```powershell
ffmpeg -i "16 - Tailwind.mp4" -c:v libx264 -preset veryfast -crf 22 -c:a aac -b:a 192k "16_fixed.mp4"
```

---
## 📋 Situs yang Bisa Didownload dengan yt-dlp + Cookies.txt
Selain YouTube member-only, yt-dlp juga mendukung banyak situs lain, antara lain:

- YouTube (video member-only & biasa)
- Vimeo
- Facebook Video
- Twitter (X)
- Instagram (tergantung jenis konten)
- TikTok
- SoundCloud
- Bilibili
- Udemy (dengan login dan cookies valid)
- Crunchyroll
- Niconico
- Dailymotion
- Twitch (video VOD)

Pastikan situs yang dituju didukung oleh yt-dlp dan kamu menggunakan cookies hasil login yang valid agar dapat mengakses konten berbayar atau terbatas.

---

## 📌 Catatan

* Gunakan `--cookies` jika playlist privat atau restricted.
* Format output `%(playlist_index)s - %(title)s.%(ext)s` membantu menjaga urutan.
* Jika terjadi error codec, gunakan opsi `-c:v libx264` untuk re-encode.
* **Windows (PowerShell)** → gunakan tanda kutip ganda `"`  
* **Linux/macOS (bash/zsh)** → bisa pakai tanda kutip tunggal `'` atau ganda `"`, asal hati-hati dengan ekspansi variabel shell.
- `coba.txt` harus berada di direktori atau folder yang sama saat menjalankan perintah.
- Jika muncul error seperti *"Join this channel to get access to members-only content"*, berarti:
  - Kamu belum login saat mengekspor cookies.
  - Atau akun kamu belum menjadi member channel tersebut.
- Gunakan format ID lain jika ingin kualitas video berbeda (lihat hasil dari `-F`).
- Pastikan cookies tidak kedaluwarsa dan kamu tidak menggunakan mode incognito saat ekspor cookies.