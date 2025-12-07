# 🚀 Quick Start Guide - Structify Music

## ⚡ Setup Cepat (5 Menit)

### **Step 1: Copy File-file Service**

Pindahkan 3 service files ke folder project lo:

```
project-lo/
├── music_service.py      ← Copy dari outputs/
├── playlist_service.py   ← Copy dari outputs/
├── player_service.py     ← Copy dari outputs/
├── app.py                ← REPLACE dengan app_upgraded.py
├── models.py             ← Sudah ada (jangan diganti!)
├── songs_repository.py   ← Sudah ada (jangan diganti!)
└── ...
```

### **Step 2: Backup & Replace app.py**

```bash
# Backup app.py lama
mv app.py app_old.py

# Copy app_upgraded.py jadi app.py
cp outputs/app_upgraded.py app.py
```

### **Step 3: Jalankan!**

```bash
python app.py
```

Buka browser: `http://localhost:5000`

---

## 🧪 Testing Checklist

### **A. Test Admin CRUD** ✅

1. **Login sebagai Admin**
   - Email: `admin@demo.com`
   - Password: `admin123`

2. **Tambah Lagu Baru**
   - Klik "Tambah Lagu Baru"
   - Isi form:
     - Title: "Test Song"
     - Artist: "Test Artist"
     - Genre: "Pop"
     - Year: 2024
     - Audio file: "test.mp3"
     - Cover file: "test.jpg"
     - Nayara Key: "happy_key"
   - Submit
   - ✅ **Cek**: Lagu muncul di admin dashboard

3. **Edit Lagu**
   - Klik "Edit" di salah satu lagu
   - Ubah title jadi "Test Song Edited"
   - Submit
   - ✅ **Cek**: Title berubah di dashboard

4. **Delete Lagu + SYNC Test (PENTING!)**
   - **Pertama**, login sebagai guest, tambah lagu ke queue
   - **Kedua**, login sebagai admin, delete lagu tersebut
   - **Ketiga**, login lagi sebagai guest, cek queue
   - ✅ **Expected**: Lagu HILANG dari queue user (auto sync!)

---

### **B. Test User Playlist** ✅

1. **Login sebagai Guest**
   - Email: `guest@demo.com`
   - Password: `guest123`

2. **Add to Queue**
   - Browse lagu di dashboard
   - Klik "Add to Queue" di beberapa lagu
   - ✅ **Cek**: `/guest/queue` - lagu masuk queue

3. **Remove from Queue**
   - Di `/guest/queue`, klik tombol "✕" di salah satu lagu
   - ✅ **Cek**: Lagu hilang dari queue

4. **Clear Queue**
   - Klik "Clear Queue"
   - Confirm
   - ✅ **Cek**: Queue kosong

---

### **C. Test Next/Prev Logic** ✅

**Scenario 1: Next di Playlist Mode**
1. Add 3 lagu ke queue: A, B, C
2. Play lagu A
3. Klik Next
4. ✅ **Expected**: Main lagu B (ikutin urutan queue)

**Scenario 2: Next di Library Mode**
1. Play lagu dari artist "Tulus"
2. Klik Next (tanpa ada queue/playlist aktif)
3. ✅ **Expected**: Main lagu lain dari Tulus (same artist)
4. Kalau ga ada Tulus lagi → lagu dengan genre sama
5. Kalau masih ga ada → lagu berikutnya di library

**Scenario 3: Prev**
1. Play lagu apa aja
2. Klik Prev
3. ✅ **Expected**: Main lagu sebelumnya di library (Doubly LL order)

---

### **D. Test Search** ✅

1. Di homepage, ketik "Tulus" di search bar
2. ✅ **Expected**: Muncul semua lagu dari Tulus
3. Ketik "Pop" 
4. ✅ **Expected**: Muncul lagu-lagu genre Pop

---

## 🐛 Troubleshooting

### **Problem 1**: Import Error
```
ImportError: No module named 'music_service'
```
**Solution**: Pastikan 3 service files ada di folder yang sama dengan app.py

---

### **Problem 2**: Lagu tidak sync terhapus
```
Admin delete lagu, tapi masih ada di queue user
```
**Solution**: 
- Cek function `get_all_playlists()` di app.py
- Pastikan playback_queue dan user_playlists masuk di dict

---

### **Problem 3**: Next/Prev tidak jalan
```
API return 404 saat klik next/prev
```
**Solution**:
- Cek apakah player_service.py sudah di-import
- Cek route `/api/next/<song_id>` ada di app.py
- Test manual: `curl http://localhost:5000/api/next/1`

---

## 📊 Demo Scenarios untuk Presentasi

### **Demo 1: Admin CRUD + Sync (5 menit)**

**Script**:
> "Pak/Bu, saya akan demo fitur admin. Pertama saya login sebagai admin."
> 
> [Login admin]
> 
> "Ini dashboard admin yang menampilkan semua lagu di library menggunakan Doubly Linked List."
> 
> [Show admin dashboard]
> 
> "Saya akan tambah lagu baru."
> 
> [Tambah lagu "Demo Song"]
> 
> "Lagu berhasil ditambahkan. Sekarang saya akan demo sinkronisasi deletion."
> 
> [Logout, login sebagai guest]
> 
> "Saya login sebagai user biasa, tambahkan lagu 'Demo Song' ke queue."
> 
> [Add to queue]
> 
> "Lagu masuk queue. Sekarang saya login lagi sebagai admin dan hapus lagu ini."
> 
> [Logout, login admin, delete "Demo Song"]
> 
> "Lagu dihapus. Sekarang saya cek queue user tadi."
> 
> [Login guest, check queue]
> 
> "Lihat Pak/Bu, lagu otomatis hilang dari queue! Ini karena function delete_song() melakukan sinkronisasi ke semua playlist."

---

### **Demo 2: Next/Prev Smart Logic (3 menit)**

**Script**:
> "Sekarang saya demo fitur next/prev dengan 2 mode."
> 
> "Mode 1: Di playlist. Saya add 3 lagu ke queue."
> 
> [Add songs A, B, C to queue]
> 
> "Saya play lagu A, lalu klik next."
> 
> [Play A, click next]
> 
> "Lihat, lagu B yang diputar. Ini karena next mengikuti urutan queue (FIFO)."
> 
> "Mode 2: Di library tanpa playlist. Saya play lagu dari Tulus."
> 
> [Play Tulus song, clear queue first]
> 
> "Saya klik next."
> 
> [Click next]
> 
> "Lihat, yang diputar lagu lain dari Tulus juga. Ini karena similarity logic: same artist punya priority tertinggi."

---

## 🎯 Key Points untuk Presentasi

1. **Doubly Linked List untuk Library**
   - Show code di `models.py` → `DoublyLinkedSongList`
   - Jelaskan kenapa butuh prev/next pointer
   - Demo operasi O(1) untuk prev/next

2. **Queue untuk Playlist**
   - Show code di `models.py` → `Playlist`
   - Jelaskan FIFO behavior
   - Demo enqueue/dequeue

3. **Service Layer Architecture**
   - Show 3 files: music_service, playlist_service, player_service
   - Jelaskan separation of concerns
   - Mudah di-maintain dan di-test

4. **Sinkronisasi Admin-User**
   - Show function `delete_song()` di music_service.py
   - Jelaskan loop semua playlists
   - Demo real-time sync

5. **Smart Next/Prev**
   - Show function `find_similar_song()` di player_service.py
   - Jelaskan priority: artist > genre > fallback
   - Demo 2 mode (playlist vs library)

---

## 📝 Checklist Sebelum Presentasi

- [ ] Backup data CSV (case ada corrupt)
- [ ] Test semua demo scenarios
- [ ] Prepare 2 browser tabs (admin + user)
- [ ] Screenshot code penting (untuk slide)
- [ ] Test koneksi internet (kalau ada video)
- [ ] Print README.md (kalau perlu)

---

## 🎬 Video Demo Outline

**Total: 5-7 menit**

1. **Intro (30 detik)**
   - Nama project
   - Struktur data yang dipakai
   - Anggota kelompok

2. **Admin CRUD (2 menit)**
   - Login admin
   - Tambah lagu
   - Edit lagu
   - Delete lagu + sync demo

3. **User Playlist (1.5 menit)**
   - Login user
   - Browse songs
   - Add to queue
   - View queue
   - Remove from queue

4. **Next/Prev Logic (2 menit)**
   - Demo mode playlist (FIFO)
   - Demo mode library (similarity)
   - Explain the logic

5. **Code Walkthrough (1 menit)**
   - Show models.py (data structures)
   - Show service layer
   - Highlight key functions

6. **Closing (30 detik)**
   - Recap
   - Challenges & solutions
   - Thank you

---

Good luck bro! 🔥🚀
