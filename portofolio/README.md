
# PORTOFOLIO INDIVIDU
## SISTEM ANTREAN PEKERJAAN DAN MONITORING PROSES LINUX

**Pilihan 5B | Anggota 3 — Status dan Exit Status**

**Nama:** Muhammad Haikal Irawan  
**Program Studi:** S1 Sistem Informasi  
**Fakultas:** Fakultas Ilmu Komputer  
**Universitas:** UPN Veteran Jakarta  
**Mata Kuliah:** Sistem Operasi

---

## 1. Pendahuluan

Proyek Sistem Antrean Pekerjaan dan Monitoring Proses Linux (Pilihan 5B) bertujuan mengelola pekerjaan komputasi sederhana secara berurutan menggunakan antrean FIFO.

Sistem kelompok dilengkapi pencatatan status pekerjaan, PID, exit status, monitoring, dan timeout.

Portofolio ini mendokumentasikan kontribusi individu pada bagian **Status dan Exit Status**.

## 2. Identitas dan Tanggung Jawab Individu

Pada proyek ini saya bertugas sebagai **Anggota 3** dengan tanggung jawab mengembangkan dan menguji mekanisme pencatatan status serta hasil akhir proses.

Tanggung jawab saya meliputi:

1. Membuat status awal `waiting` untuk pekerjaan yang didaftarkan.
2. Mengubah status menjadi `running` ketika proses mulai dijalankan.
3. Mencatat Process ID (PID) dari proses yang dijalankan di background.
4. Mengambil exit status proses setelah proses selesai menggunakan perintah `wait`.
5. Menentukan status akhir `success` jika exit status bernilai 0 dan `failed` jika bukan 0.
6. Menampilkan kembali informasi pekerjaan melalui perintah `show`.

## 3. Landasan Teori

### 3.1 Process ID (PID)

PID adalah nomor identitas yang diberikan Linux kepada suatu proses. PID membantu membedakan dan melacak proses yang sedang berjalan.

### 3.2 Exit Status

Exit status adalah kode hasil eksekusi perintah atau proses.

- Nilai `0` menandakan proses berhasil.
- Nilai selain `0` menandakan proses berakhir dengan kondisi gagal atau error.

### 3.3 Status Pekerjaan

| Status | Penjelasan |
|---|---|
| `waiting` | Pekerjaan telah didaftarkan tetapi belum mulai dieksekusi. |
| `running` | Pekerjaan sedang dieksekusi. |
| `success` | Pekerjaan selesai dengan exit status 0. |
| `failed` | Pekerjaan selesai dengan exit status bukan 0. |

### 3.4 Perintah wait

Pada Bash, perintah `wait` menunggu proses background yang ditentukan selesai dan mengembalikan kode keluarnya. Nilai tersebut dapat disimpan ke dalam variabel, misalnya `rc`.

## 4. Lingkungan dan Struktur Implementasi

Implementasi dan pengujian dilakukan pada Ubuntu yang dijalankan melalui UTM di Mac.

Direktori kerja proyek yang terlihat pada terminal adalah `~/project-5b`.

Struktur pengujian individu:

```text
project-5b/
├── scripts/
│   └── status.sh
├── data/
│   └── jobs/
└── logs/
```

File utama yang diuji adalah `scripts/status.sh`. Dalam repository kelompok, file status berada di direktori `monitoring/status.sh`.

Berdasarkan penggunaan pada terminal, script menyediakan operasi berikut:

| Operasi | Fungsi |
|---|---|
| `init JOB_ID` | Membuat catatan awal pekerjaan dengan status waiting. |
| `start JOB_ID PID` | Mencatat proses mulai berjalan dan menyimpan PID. |
| `finish JOB_ID EXIT_STATUS` | Mencatat hasil akhir berdasarkan exit status. |
| `show JOB_ID` | Menampilkan JOB_ID, STATUS, PID, EXIT_STATUS, dan UPDATED_AT. |

## 5. Prosedur Pengujian

Pengujian dilakukan menggunakan proses dummy Bash untuk memeriksa perubahan status dan pencatatan exit status.

### 5.1 Pengujian Positif — job001

Pengujian positif menggunakan proses dengan exit status `0`.

Perintah pengujian:

```bash
./scripts/status.sh init job001

bash -c 'exit 0' &
pid=$!

./scripts/status.sh start job001 "$pid"

wait "$pid"
rc=$?

./scripts/status.sh finish job001 "$rc"
./scripts/status.sh show job001
```

Hasil yang tercatat:

- Job ID: `job001`
- PID: `4907`
- Status akhir: `success`
- Exit status: `0`

Pengujian ini menunjukkan bahwa proses yang selesai dengan exit status 0 dicatat sebagai berhasil.

### 5.2 Pengujian Negatif — job002

Pengujian negatif menggunakan proses yang berakhir dengan exit status `7`.

Perintah pengujian:

```bash
./scripts/status.sh init job002

bash -c 'exit 7' &
pid=$!

./scripts/status.sh start job002 "$pid"

wait "$pid"
rc=$?

./scripts/status.sh finish job002 "$rc"
./scripts/status.sh show job002
```

Hasil yang tercatat:

- Job ID: `job002`
- PID: `4981`
- Status akhir: `failed`
- Exit status: `7`

Pengujian ini menunjukkan bahwa script dapat membedakan proses berhasil dan proses yang berakhir dengan kode bukan nol.

### 5.3 Pengujian Perubahan Status — job004

Pengujian job004 menggunakan proses dummy `sleep` selama 15 detik.

Saat diperiksa, status tercatat `running` dengan PID `6016`.

Setelah proses selesai dan exit status diambil, catatan akhir berubah menjadi:

- Job ID: `job004`
- PID: `6016`
- Status akhir: `success`
- Exit status: `0`

Pengujian ini memperlihatkan perubahan status dari `running` menjadi `success`.

### 5.4 Pengujian Ulang — job003

Pengujian job003 sebelumnya menunjukkan PID kosong karena proses tidak dicatat melalui operasi `start` pada percobaan awal.

Data job003 dan job004 kemudian dibersihkan, lalu job003 dibuat dan diuji ulang.

Perintah pengujian:

```bash
./scripts/status.sh init job003

bash -c 'sleep 5; exit 0' &
pid=$!

./scripts/status.sh start job003 "$pid"

wait "$pid"
rc=$?

./scripts/status.sh finish job003 "$rc"
./scripts/status.sh show job003
```

Hasil pengujian ulang:

- Job ID: `job003`
- PID: `6122`
- Status akhir: `success`
- Exit status: `0`

Pada saat perintah `show` dijalankan, proses `sleep 5` sudah selesai. Bukti status `running` yang terlihat secara eksplisit tersedia pada pengujian job004.

## 6. Ringkasan Hasil Pengujian

| Job ID | Skenario | PID | Status Akhir | Exit Status | Kesimpulan |
|---|---|---:|---|---:|---|
| job001 | Proses berhasil | 4907 | success | 0 | Positif — lulus |
| job002 | Proses exit 7 | 4981 | failed | 7 | Negatif — lulus |
| job003 | sleep 5; exit 0 | 6122 | success | 0 | Pengujian ulang — lulus |
| job004 | sleep 15; exit 0 | 6016 | success | 0 | Running lalu success — lulus |

Keempat skenario di atas merupakan bukti uji mekanisme status dan exit status secara mandiri.

**Catatan:** File `job004.status` kemudian dihapus saat penataan ulang data. Hasil pengujian job004 tetap didokumentasikan melalui screenshot sebagai bukti.

## 7. Dokumentasi Bukti Terminal

Bukti terminal dari pengujian individu mencakup:

1. **Pengujian job001 dan job002**  
   Memperlihatkan job001 berakhir `success` dengan PID 4907 dan exit status 0, sedangkan job002 berakhir `failed` dengan PID 4981 dan exit status 7.

2. **Pengujian status running pada job004**  
   Memperlihatkan job004 berstatus `running` dengan PID 6016. Setelah proses selesai, catatan akhir menunjukkan `success` dan exit status 0.

3. **Pengujian ulang job003 dan pemeriksaan direktori data/jobs**  
   Memperlihatkan job003 berhasil dicatat dengan PID 6122, status `success`, dan exit status 0. Direktori `data/jobs` setelah pembersihan berisi `job001.status`, `job002.status`, dan `job003.status`.

## 8. Analisis dan Pembahasan

Hasil pengujian memperlihatkan bahwa pencatatan status perlu dilakukan pada tahapan yang sesuai.

- Operasi `init` membuat catatan awal.
- Operasi `start` menyimpan PID saat proses mulai berjalan.
- Perintah `wait` menunggu proses selesai sekaligus memperoleh exit status.
- Operasi `finish` memperbarui status akhir berdasarkan exit status.

Jika operasi `start` dilewati, informasi PID tidak akan tercatat pada hasil akhir, sebagaimana terlihat pada percobaan awal job003. Pengujian ulang memperbaiki urutan tersebut.

### Integrasi dengan Sistem Kelompok

Pada repository kelompok, kode `anggota2/tambah_antrean.sh` memanggil operasi `init` ketika pekerjaan ditambahkan ke antrean.

Kode `anggota2/worker.sh` juga memanggil operasi `start` sebelum menjalankan pekerjaan, mengambil exit code setelah proses selesai, kemudian memanggil operasi `finish`.

Hal tersebut menunjukkan adanya pemanggilan mekanisme status dari kode worker dan penambahan antrean.

Namun, pengujian end-to-end seluruh alur FIFO bersama worker belum dinyatakan selesai dalam dokumentasi ini. Pengujian integrasi perlu dilakukan untuk memastikan seluruh proses berjalan sesuai rancangan.

Pengujian timeout, lock worker, monitoring CPU/memori, pembatasan akses, dan aktivitas Docker juga perlu dibuktikan melalui pengujian sistem kelompok sesuai pembagian tugas.

## 9. Kesimpulan

Berdasarkan implementasi dan pengujian yang terdokumentasi, mekanisme `status.sh` dapat membuat catatan pekerjaan, menyimpan PID, menampilkan status, serta menentukan status akhir berdasarkan exit status.

Pengujian positif menghasilkan `success` dengan exit status 0, sedangkan pengujian negatif menghasilkan `failed` dengan exit status 7.

Pengujian ulang job003 juga berhasil mencatat PID 6122 dan status `success`.

Dengan demikian, bagian inti **Status dan Exit Status** yang menjadi tanggung jawab individu telah diuji secara mandiri. Kode repository kelompok juga telah menunjukkan pemanggilan operasi status dari proses penambahan antrean dan worker, sementara verifikasi end-to-end masih perlu dilakukan.
