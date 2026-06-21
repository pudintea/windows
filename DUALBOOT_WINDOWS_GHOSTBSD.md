Karena Windows Anda sudah menggunakan **UEFI** dan sudah ada **100 GB unallocated space**, maka skenario yang paling aman adalah:

* Biarkan partisi Windows apa adanya.
* Gunakan **EFI System Partition (ESP)** yang sudah dibuat Windows.
* Instal GhostBSD ke ruang kosong 100 GB.
* GhostBSD akan menambahkan entri boot ke UEFI.

Berikut panduan lengkap dari awal sampai akhir.

# Tahap 1 — Persiapan di Windows

## 1. Pastikan ruang 100 GB benar-benar kosong

Buka:

```text
Win + X
Disk Management
```

Pastikan terlihat:

```text
100 GB Unallocated
```

Jika masih berupa drive D:, E:, atau partisi lain, jangan lanjut dulu. Hapus partisi tersebut sehingga statusnya menjadi **Unallocated**.

---

## 2. Matikan Fast Startup Windows

Ini penting agar partisi Windows tidak terkunci.

Buka:

```text
Control Panel
→ Power Options
→ Choose what the power buttons do
→ Change settings that are currently unavailable
```

Hilangkan centang:

```text
Turn on Fast Startup
```

Kemudian:

```text
Save Changes
```

---

## 3. Backup data penting

Walaupun risikonya kecil, tetap backup:

* Dokumen
* Foto
* Project kerja
* File penting lainnya

---

# Tahap 2 — Membuat USB Installer GhostBSD

## 1. Download ISO GhostBSD

Dari:

[GhostBSD Download Page](https://ghostbsd.org/download?utm_source=chatgpt.com)

Pilih versi terbaru yang stabil.

---

## 2. Download Rufus

Dari:

[Rufus Official Site](https://rufus.ie?utm_source=chatgpt.com)

---

## 3. Buat USB Bootable

Colok flashdisk minimal 8 GB.

Buka Rufus:

```text
Device            : Flashdisk Anda
Boot Selection    : ISO GhostBSD
Partition Scheme  : GPT
Target System     : UEFI
File System       : FAT32
```

Klik:

```text
START
```

Tunggu selesai.

---

# Tahap 3 — Boot ke Installer GhostBSD

Restart komputer.

Masuk Boot Menu:

Biasanya:

```text
F12
```

atau

```text
Esc
```

atau

```text
F11
```

Pilih:

```text
UEFI: Nama Flashdisk
```

Jangan pilih mode Legacy.

---

# Tahap 4 — Masuk Live Environment GhostBSD

Setelah boot:

```text
Try GhostBSD
```

atau desktop GhostBSD akan langsung muncul.

Tunggu sampai desktop selesai loading.

Kemudian klik:

```text
Install GhostBSD
```

---

# Tahap 5 — Pilih Bahasa dan Keyboard

Pilih:

```text
Language : Indonesian atau English
Keyboard : US
```

Klik Next.

---

# Tahap 6 — Partisi Disk

Ini bagian paling penting.

Installer akan menampilkan disk Anda.

Misalnya tampil seperti:

```text
EFI System Partition      260 MB
Microsoft Reserved        16 MB
Windows C:                400 GB
Recovery                  600 MB
Unallocated               100 GB
```

### Jangan sentuh:

* EFI
* MSR
* Windows
* Recovery

Gunakan hanya:

```text
100 GB Unallocated
```

---

# Tahap 7 — Membuat Partisi GhostBSD

Pilih:

```text
Manual Partitioning
```

atau

```text
Custom Partitioning
```

### Root Partition

Buat partisi:

```text
Mount Point : /
Filesystem  : ZFS
Size        : 92 GB
```

Alasan:

* ZFS adalah filesystem default FreeBSD/GhostBSD.
* Snapshot lebih baik.
* Lebih modern.

---

### Swap Partition

Buat:

```text
Type : Swap
Size : 8 GB
```

Jika RAM Anda:

| RAM   | Swap |
| ----- | ---- |
| 8 GB  | 8 GB |
| 16 GB | 8 GB |
| 32 GB | 8 GB |

Untuk penggunaan normal 8 GB swap sudah cukup.

---

# Tahap 8 — EFI Partition

Installer biasanya mendeteksi:

```text
EFI System Partition
```

yang sudah dibuat Windows.

Gunakan partisi EFI tersebut.

Jangan:

* format ulang
* delete
* create EFI baru

Yang benar:

```text
Mount Point : /boot/efi
```

atau cukup pilih opsi:

```text
Use Existing EFI Partition
```

tergantung versi installer.

---

# Tahap 9 — Instalasi Sistem

Klik:

```text
Install
```

Proses biasanya:

```text
10–30 menit
```

tergantung SSD/HDD.

---

# Tahap 10 — Membuat User

Saat diminta:

### Hostname

Contoh:

```text
ghostbsd
```

---

### Username

Contoh:

```text
andi
```

---

### Root Password

Masukkan password administrator.

Simpan baik-baik.

---

# Tahap 11 — Selesai dan Reboot

Ketika muncul:

```text
Installation Complete
```

Klik:

```text
Restart
```

Cabut flashdisk.

---

# Tahap 12 — Cek Boot UEFI

Saat komputer menyala:

Masuk BIOS/UEFI.

Cari menu:

```text
Boot Order
```

Biasanya akan muncul:

```text
Windows Boot Manager
GhostBSD
```

Set:

```text
GhostBSD
```

sebagai boot pertama.

Save & Exit.

---

# Tahap 13 — Mengakses Windows

Jika setelah boot masuk GhostBSD dan belum ada menu pilihan OS:

Di GhostBSD buka Terminal:

```bash
efibootmgr
```

atau gunakan menu boot motherboard:

```text
F12
```

untuk memilih:

```text
Windows Boot Manager
```

---

# Tahap 14 — Membuat Menu Boot Otomatis (Direkomendasikan)

Supaya saat startup muncul pilihan:

```text
GhostBSD
Windows 11
```

instal boot manager UEFI:

rEFInd

Situs resmi:

[rEFInd Official Site](https://www.rodsbooks.com/refind/?utm_source=chatgpt.com)

rEFInd biasanya otomatis mendeteksi:

* Windows
* GhostBSD
* Linux lain jika ada

dan menampilkan menu grafis saat boot.

---

# Gambaran Akhir Partisi

Setelah selesai, struktur disk kira-kira menjadi:

```text
EFI System Partition     260 MB
Microsoft Reserved        16 MB
Windows C:              xxx GB
Recovery                 600 MB
GhostBSD (ZFS)            92 GB
Swap                       8 GB
```

Sebelum mulai instalasi, saya sarankan Anda kirim **screenshot Disk Management Windows** (tampilan seluruh disk, bukan hanya sebagian). Dengan melihat layout partisi yang sebenarnya, saya bisa memastikan langkah partisi EFI dan ZFS yang tepat sehingga risiko salah partisi hampir nol.
