# 🎵 Structify Music - Tugas Besar Struktur Data

## 📋 Deskripsi Proyek

Aplikasi pemutar musik berbasis web yang mengimplementasikan **Doubly Linked List** untuk library lagu dan **Queue (Singly Linked List)** untuk playlist pemutaran. Proyek ini dibuat untuk memenuhi tugas besar mata kuliah Struktur Data.

---

## 🎯 Fitur yang Diimplementasikan

### **A. Fitur Admin** ✅
1. ✅ **Tambah lagu baru** ke library
2. ✅ **Lihat semua lagu** di library
3. ✅ **Edit data lagu** yang sudah ada
4. ✅ **Hapus lagu** dari library
   - **SYNC OTOMATIS**: Ketika admin hapus lagu, akan otomatis terhapus dari **SEMUA playlist user**

### **B. Fitur User** ✅
1. ✅ **Mencari lagu** berdasarkan judul atau artis
2. ✅ **Play/Stop lagu** dengan notifikasi
3. ✅ **Next/Prev lagu** dengan 2 mode:
   - **Mode Playlist**: Next/prev ikutin urutan queue
   - **Mode Library**: Next/prev cari lagu mirip (same artist > same genre)
4. ✅ **Kelola playlist**:
   - Tambah lagu ke playlist
   - Hapus lagu dari playlist
   - Lihat isi playlist
   - Clear seluruh playlist

---

## 🏗️ Struktur Data yang Digunakan

### **1. Doubly Linked List (Library Lagu)**

**File**: `models.py` → `DoublyLinkedSongList`

**Mengapa Doubly Linked List?**
- ✅ **Efisiensi prev/next**: O(1) untuk akses prev/next node
- ✅ **Cocok untuk library**: User bisa browse bolak-balik dengan cepat
- ✅ **Update mudah**: Kalau admin edit lagu, referensi di semua playlist ikut berubah (karena pakai reference ke object yang sama)

**Operasi yang diimplementasikan**:
```python
- append(song)              # O(1) - tambah di akhir
- find_by_id(song_id)       # O(n) - cari lagu
- remove_by_id(song_id)     # O(n) - hapus lagu
- update_song(song_id)      # O(n) untuk find, O(1) untuk update
- get_prev_next_by_id()     # O(n) untuk find, O(1) untuk akses prev/next
```

---

### **2. Queue / Singly Linked List (Playlist)**

**File**: `models.py` → `Playlist`

**Mengapa Queue?**
- ✅ **FIFO**: Lagu yang ditambah duluan, diputar duluan
- ✅ **Efisien**: enqueue O(1), dequeue O(1)
- ✅ **Natural playlist behavior**: Sesuai dengan cara user mendengarkan musik

**Operasi yang diimplementasikan**:
```python
- enqueue(song)             # O(1) - tambah lagu ke antrian
- dequeue()                 # O(1) - ambil lagu pertama
- remove_song_by_id()       # O(n) - hapus lagu tertentu
- clear()                   # O(1) - kosongkan playlist
```

---

## 📁 Struktur File & Penjelasan

```
structify-music/
│
├── app_upgraded.py          # Main Flask application (GUNAKAN INI!)
├── models.py                # Data structures (DoublyLinkedList, Queue/Playlist)
├── songs_repository.py      # Load/save lagu dari CSV
│
├── music_service.py         # Service layer untuk Admin CRUD
├── playlist_service.py      # Service layer untuk User playlist
├── player_service.py        # Service layer untuk Next/Prev logic
│
├── templates/               # HTML templates
│   ├── base.html
│   ├── admin_dashboard.html
│   ├── admin_song_form.html
│   ├── guest_dashboard.html
│   ├── guest_queue.html
│   └── ...
│
├── static/                  # CSS, JS, audio, covers
│   ├── css/style.css
│   ├── js/app.js
│   ├── audio/
│   └── covers/
│
└── data/
    └── songs.csv            # Database lagu (CSV)
```

---

## 🧠 Penjelasan Service Layer (PENTING untuk Presentasi!)

### **1. music_service.py** - Admin CRUD Operations

Menghandle semua operasi admin terhadap library lagu.

**Key Functions**:
```python
add_song(library, data)
    → Tambah lagu baru ke Doubly Linked List
    → Auto-generate ID (auto-increment)
    → Persist ke CSV

update_song(library, song_id, data)
    → Update data lagu yang sudah ada
    → Karena pakai reference, otomatis update di semua playlist

delete_song(library, song_id, all_playlists)
    → Hapus dari library (Doubly Linked List)
    → SYNC: Hapus juga dari SEMUA playlist user
    → Persist ke CSV
```

**Contoh Alur Delete (untuk dijelaskan ke dosen)**:
1. Admin klik delete lagu ID 5
2. Function `delete_song()` dipanggil
3. Hapus dari `song_library` (Doubly Linked List)
4. Loop semua playlists (`playback_queue`, `favorites`, dll)
5. Hapus song ID 5 dari tiap playlist
6. Save perubahan ke CSV
7. ✅ **Semua page otomatis bersih dari lagu yang dihapus**

---

### **2. playlist_service.py** - User Playlist Operations

Menghandle operasi user terhadap playlist/queue.

**Key Functions**:
```python
add_to_queue(library, queue, song_id)
    → Cari lagu di library (Doubly Linked List)
    → Enqueue ke queue (O(1))
    
remove_from_queue(queue, song_id)
    → Hapus lagu dari queue
    → Bisa hapus multiple occurrences

clear_queue(queue)
    → Kosongkan semua lagu di queue
```

---

### **3. player_service.py** - Next/Prev Logic (REQUIREMENT TUGAS!)

Ini yang paling penting untuk dijelaskan ke dosen!

**Smart Next/Prev Logic**:

```python
get_next_track(library, queue, current_song_id, is_in_playlist_mode)
    
    IF user sedang di playlist:
        → Return next song di queue (ikutin urutan FIFO)
    
    ELSE (user di library):
        → Cari lagu MIRIP dengan current song
        → Priority: same artist > same genre > fallback next

find_similar_song(library, current_song_id)
    1. Cari lagu dengan SAME ARTIST (highest priority)
    2. Kalau ga ada, cari SAME GENRE
    3. Kalau masih ga ada, fallback ke next song di library
```

**Contoh untuk Presentasi**:

**Scenario 1**: User play lagu "Cahaya - Tulus" **di PLAYLIST**
- Klik next → main lagu berikutnya di queue (misal "Plastic - Pamungkas")
- **Logika**: Ikutin urutan queue (FIFO)

**Scenario 2**: User play lagu "Cahaya - Tulus" **di LIBRARY**
- Klik next → cari lagu lain dari Tulus (same artist)
- Kalau ga ada Tulus lagi, cari lagu Pop lainnya (same genre)
- **Logika**: Similarity-based recommendation

---

## 🔄 Sinkronisasi Admin → User (CRITICAL!)

**Masalah yang diselesaikan**:
> "Kalau admin hapus/edit lagu, gimana caranya update di semua page user tanpa refresh manual?"

**Solusi**:

### **1. Update Lagu (Edit)**
- Admin edit lagu ID 5 → ubah title, artist, dll
- Karena library pakai **Doubly Linked List dengan REFERENCE ke object yang sama**
- Semua playlist yang punya lagu ID 5 otomatis ikut berubah (shared reference!)
- ✅ **Tidak perlu sync manual**

### **2. Delete Lagu**
- Admin delete lagu ID 5
- Function `delete_song()` akan:
  1. Hapus dari library (Doubly Linked List)
  2. Loop **SEMUA playlists** (playback_queue, favorites, dll)
  3. Hapus lagu ID 5 dari tiap playlist
- ✅ **Sinkron ke semua page**

**Code snippet (untuk dijelaskan)**:
```python
def delete_song(library, song_id, all_playlists):
    # Hapus dari library
    removed = library.remove_by_id(song_id)
    
    if removed:
        # SYNC: Hapus dari SEMUA playlist
        for playlist in all_playlists.values():
            playlist.remove_song_by_id(song_id)
        
        # Persist
        save_songs_to_csv(library)
    
    return removed
```

---

## 🚀 Cara Menjalankan Program

### **Prerequisites**
- Python 3.8+
- Flask

### **Installation**
```bash
# 1. Clone repository
git clone <repo-url>
cd structify-music

# 2. Install dependencies
pip install flask

# 3. Jalankan aplikasi
python app_upgraded.py

# 4. Buka browser
http://localhost:5000
```

### **Demo Accounts**
```
Admin:
Email: admin@demo.com
Password: admin123

Guest:
Email: guest@demo.com
Password: guest123
```

---

## 🎓 Penjelasan untuk Dosen (Talking Points)

### **1. Mengapa Doubly Linked List untuk Library?**
> "Pak/Bu, kami pakai Doubly Linked List untuk library karena user perlu bisa browse lagu bolak-balik dengan cepat. Dengan prev dan next pointer, kami bisa akses lagu sebelumnya/berikutnya dalam O(1) tanpa perlu traversal dari awal."

### **2. Mengapa Queue untuk Playlist?**
> "Untuk playlist, kami pakai Queue (Singly Linked List) karena perilaku FIFO sangat natural untuk musik. Lagu yang ditambahkan duluan, diputar duluan. Operasi enqueue dan dequeue juga sangat efisien O(1)."

### **3. Bagaimana Next/Prev Logic?**
> "Kami implement 2 mode sesuai requirement:
> - **Di playlist**: Next/prev ikutin urutan queue
> - **Di library**: Next/prev cari lagu mirip (same artist lebih prioritas daripada same genre)
> 
> Ini memanfaatkan struktur Doubly Linked List untuk akses cepat."

### **4. Bagaimana Sinkronisasi Admin-User?**
> "Ketika admin hapus lagu, function delete_song() akan:
> 1. Hapus dari library (source of truth)
> 2. Loop semua playlist user
> 3. Hapus lagu tersebut dari tiap playlist
> 
> Dengan begini, lagu yang dihapus admin otomatis hilang dari semua page user."

---

## 📊 Kompleksitas Waktu

| Operasi | Struktur Data | Kompleksitas |
|---------|---------------|--------------|
| Tambah lagu ke library | Doubly LL | O(1) |
| Hapus lagu dari library | Doubly LL | O(n) |
| Cari lagu by ID | Doubly LL | O(n) |
| Prev/next di library | Doubly LL | O(1) setelah find |
| Enqueue playlist | Queue | O(1) |
| Dequeue playlist | Queue | O(1) |
| Hapus lagu dari playlist | Queue | O(n) |

---

## 🐛 Kendala & Solusi

### **Kendala 1**: Prev tidak bisa di Queue (Singly Linked List)
**Solusi**: Untuk prev button, fallback ke library order (Doubly Linked List)

### **Kendala 2**: Sinkronisasi deletion ke banyak playlist
**Solusi**: Buat helper function `get_all_playlists()` yang return dict semua playlists, lalu loop untuk hapus

### **Kendala 3**: Update lagu harus reflect di semua playlist
**Solusi**: Gunakan reference ke object yang sama (bukan copy), jadi update sekali langsung propagate

---

## 👥 Anggota Kelompok

- [Nama Anggota 1] - [NRP]
- [Nama Anggota 2] - [NRP]
- [Nama Anggota 3] - [NRP]

---

## 📚 Referensi

1. Materi kuliah Struktur Data - Doubly Linked List
2. Materi kuliah Struktur Data - Queue & Singly Linked List
3. Flask Documentation - https://flask.palletsprojects.com/
4. Python Data Structures - https://docs.python.org/3/tutorial/datastructures.html

---

## 🎯 Kesimpulan

Proyek ini berhasil mengimplementasikan:
- ✅ Doubly Linked List untuk library lagu dengan operasi CRUD lengkap
- ✅ Queue untuk playlist dengan perilaku FIFO
- ✅ Smart next/prev logic sesuai requirement tugas
- ✅ Sinkronisasi admin-user yang robust
- ✅ Clean service layer architecture yang mudah dijelaskan

**Lesson Learned**:
- Pemilihan struktur data sangat penting untuk efisiensi operasi
- Service layer membantu separation of concerns
- Sinkronisasi data antar komponen perlu dipikirkan sejak awal

---

**Repository**: [Link ke GitHub]
**Video Demo**: [Link ke YouTube]
**Laporan Lengkap**: [Link ke PDF]
