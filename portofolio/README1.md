# Portofolio Individu — Project Akhir Sistem Operasi (Pilihan 5B)

## 1. Identitas Mahasiswa
* **Nama:** Haikal
* **NIM:** *2510512185*
* **Peran:** Anggota 3 — Pelacakan PID, Status Proses, & Penangkapan *Exit Code*
* **Program Studi:** S1 Sistem Informasi — FIK UPN Veteran Jakarta

---

## 2. Tautan Repositori & Daftar Anggota
* **Repositori Utama:** `https://github.com/kelompok-sistem-antrean-linux/sistem_antrean_linux`
* **Repositori Fork Pribadi:** `https://github.com/<username-github-haikal>/sistem_antrean_linux`

| Peran | Nama | Fokus Pengerjaan |
| :--- | :--- | :--- |
| Anggota 1 | Rizal | Validasi input pengajuan tugas & daftar putih (*whitelist*) (`submit_job.sh`) |
| Anggota 2A | Ammy | Pengurutan antrean FIFO (`worker.sh`) |
| Anggota 2B | Jennar | Penguncian antrean dengan `flock` agar tidak jalan ganda (`worker.sh`) |
| **Anggota 3** | **Haikal** | **Pelacakan PID anak, status proses, *exit code*, & penampil status (`cek_status.sh`)** |
| Anggota 4 | Abe | Pembatasan durasi kerja (`timeout`) & sinyal penghentian proses (`worker.sh`) |
| Anggota 5 | Dwi | Pemantauan CPU/RAM, laporan akhir (`monitor_report.sh`), izin grup, & Docker |

---

## 3. Kontribusi Teknis Pribadi

Pada proyek ini, saya mengerjakan bagian pengawasan jalannya tugas di `scripts/worker.sh` dan membuat skrip bantu `scripts/cek_status.sh`:

1. **Menjalankan Tugas di Latar Belakang & Mencatat PID:** Saat `worker.sh` mengeksekusi tugas, prosesnya dijalankan di latar belakang (`&`) supaya nomor identitas prosesnya (*PID*) bisa langsung ditangkap dengan `$!` dan dicatat ke log.
2. **Menunggu Proses & Mengecek Hasil Akhir (`exit code`):** Skrip induk menunggu tugas hingga benar-benar selesai menggunakan `wait`, lalu membaca kode keluarnya (`$?`). Jika nilainya `0`, tiket dipindahkan ke folder `done/` dengan status `SUCCESS`. Jika bukan `0`, tiket dipindahkan ke `failed/` dengan status `FAILED`.
3. **Membuat Penampil Tabel Status (`scripts/cek_status.sh`):** Saya membuat skrip `cek_status.sh` memakai perintah `column` agar isi data `logs/jobs_status.csv` (mulai dari ID tugas, PID, kode keluar, sampai waktu pengerjaan) bisa dibaca dengan rapi di terminal.

**Kode `scripts/cek_status.sh`:**
```bash
#!/bin/bash
source "$(dirname "$0")/../config/queue.conf"
echo "=== TABEL STATUS PEKERJAAN ==="
column -t -s',' "$STATUS_DB"
