4. Tautan Commit
Branch: feat/anggota3-status-pid (sudah digabung ke main)

Riwayat Commit: https://github.com/kelompok-sistem-antrean-linux/sistem_antrean_linux/commits/main

File Bukti Uji: evidence/haikal-hasil.txt

5. Langkah & Hasil Pengujian
Saya menguji modul ini dengan dua skenario: satu tugas normal yang berhasil (hitung_prima.sh) dan satu tugas yang memang dirancang gagal (simulasi_gagal.sh).

Bash
cd ~/proyek-so-5b
bash scripts/reset_env.sh
bash scripts/submit_job.sh hitung_prima.sh 1
bash scripts/submit_job.sh simulasi_gagal.sh 1
bash scripts/worker.sh
bash scripts/cek_status.sh
Hasil yang didapat:

hitung_prima.sh: Berjalan normal, PID proses anak tercatat, menghasilkan EXIT_CODE = 0, dan status berubah menjadi SUCCESS (tiket masuk ke queue/done/).

simulasi_gagal.sh: Berhenti dengan galat simulasi, PID proses anak tercatat, menghasilkan EXIT_CODE = 42, dan status berubah menjadi FAILED (tiket masuk ke queue/failed/).

6. Analisis Konsep Sistem Operasi
Siklus Hidup Proses: Setiap tugas melewati tahapan yang jelas seperti teori proses di OS: dari siap menunggu (WAITING), sedang diproses CPU (RUNNING), hingga selesai (SUCCESS atau FAILED).

Hubungan Parent dan Child Process: Saat worker.sh menjalankan skrip tugas di latar belakang, sistem melakukan fork dan exec untuk membuat proses anak baru. Variabel $! memudahkan proses induk mencatat PID anak tersebut.

Fungsi wait & Mencegah Zombie Process: Perintah wait "$CHILD_PID" membuat worker.sh menunggu proses anak sampai tuntas dan langsung mengambil exit code-nya. Cara ini mencegah sisa proses anak menggantung sebagai zombie process di tabel proses Linux.

7. Kendala & Solusi Saat Pengerjaan
Kendala: Saat menggabungkan kode worker.sh dengan anggota lain, pembacaan variabel $? sempat rawan tertimpa jika ada perintah lain (seperti echo) yang dijalankan sebelum nilainya disimpan. Selain itu, tampilan file CSV mentah di terminal terlihat berantakan.

Solusi: Nilai $? langsung disimpan ke variabel EXIT_CODE tepat satu baris setelah perintah wait, dan saya membuat scripts/cek_status.sh dengan column -t -s',' supaya tampilan tabelnya sejajar.

8. Refleksi Pembelajaran
Lewat bagian ini, saya jadi lebih paham bagaimana cara Linux membuat proses anak, melacak PID, membaca status berhasil atau gagalnya suatu program lewat exit code, serta pentingnya memakai wait agar sistem tetap bersih tanpa zombie process. Saya juga belajar cara berkolaborasi menyatukan kode dalam satu repositori GitHub bersama tim.
