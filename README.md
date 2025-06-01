# 🎥 Cara Download Video dari Berbagai Situs dengan yt-dlp Menggunakan Cookies — Solusi Pengganti IDM

Panduan singkat untuk mendownload video, termasuk video khusus member atau konten berbayar, menggunakan `yt-dlp`, `ffmpeg`, dan `cookies.txt`.

## 🔧 Langkah-langkah

1. **Install ffmpeg di windows**  
   Jalankan perintah di bawah ini di CMD atau PowerShell:
   ```bash
   winget install ffmpeg
   ```

2. **Install Python**  
   Download dari [https://www.python.org](https://www.python.org) dan pastikan `pip` sudah termasuk.

3. **Install yt-dlp via pip**
   ```bash
   pip install -U yt-dlp
   ```

4. **Install ekstensi Get cookies.txt di browser**  
   - Chrome/Edge: [Get cookies.txt](https://chrome.google.com/webstore/detail/get-cookiestxt/lmjcdiepejehfhakfmbocgpoljhfledg)  
   - Firefox: [Get cookies.txt](https://addons.mozilla.org/en-US/firefox/addon/cookies-txt/)  
   Setelah itu:
   - Login ke akun YouTube yang sudah menjadi member channel
   - Buka video yang ingin diunduh
   - Klik ekstensi → ekspor cookies → simpan sebagai `cookies.txt`

5. **Lihat format video yang tersedia**  
   Jika video publik biasa:
   ```bash
   yt-dlp -F <link-video>
   ```
   Untuk video khusus member atau yang butuh login::
   ```bash
   yt-dlp -F --cookies cookies.txt <link-video>
   ```

6. **Download video dan audio**
   Misalnya, setelah melihat format ID kamu mau unduh:
   - Audio: `140`
   - Video: `137`  
   Maka gunakan perintah:
   ```bash
   yt-dlp -f "140+137" --merge-output-format mp4 --cookies cookies.txt <link-video>
   ```
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

## ⚠️ Catatan

- `cookies.txt` harus berada di direktori atau folder yang sama saat menjalankan perintah.
- Jika muncul error seperti *"Join this channel to get access to members-only content"*, berarti:
  - Kamu belum login saat mengekspor cookies.
  - Atau akun kamu belum menjadi member channel tersebut.
- Gunakan format ID lain jika ingin kualitas video berbeda (lihat hasil dari `-F`).
- Pastikan cookies tidak kedaluwarsa dan kamu tidak menggunakan mode incognito saat ekspor cookies.
