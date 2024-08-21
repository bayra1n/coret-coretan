Untuk mengkonfigurasi SFTP dengan cara yang memungkinkan Anda untuk mounting folder dari Windows Server A (192.168.1.2) ke Windows Server B (192.168.1.3) tanpa menggunakan Samba dan mengubah port SFTP ke 4522, Anda dapat mengikuti langkah-langkah berikut. Ini melibatkan setup OpenSSH di Windows dan mengkonfigurasi SFTP untuk melakukan mounting folder di jaringan.

### Langkah-langkah Konfigurasi:

#### **1. Instal dan Konfigurasi OpenSSH Server di Windows Server A (192.168.1.2)**
1. **Instal OpenSSH Server:**
   - Buka *Settings* > *Apps* > *Optional Features* > *Add a feature*.
   - Cari *OpenSSH Server* dan instal.

2. **Konfigurasi OpenSSH untuk SFTP:**
   - Buka file konfigurasi OpenSSH. File konfigurasi ini biasanya terletak di `C:\ProgramData\ssh\sshd_config`.
   - Buka file `sshd_config` menggunakan text editor seperti Notepad.

3. **Ubah Port SSH:**
   - Cari baris yang mengatakan `#Port 22` dan ubah menjadi `Port 4522`.
   - Ini mengubah port SSH dari default 22 ke 4522.

4. **Konfigurasi SFTP:**
   - Dalam file `sshd_config`, pastikan ada entri untuk `Subsystem sftp` seperti ini:
     ```bash
     Subsystem sftp internal-sftp
     ```
   - Untuk memastikan keamanan, tambahkan konfigurasi berikut di bagian bawah file:
     ```bash
     Match User sftpuser
         ChrootDirectory D:/wadaw
         ForceCommand internal-sftp
         AllowTcpForwarding no
         X11Forwarding no
     ```
     Ini mengkonfigurasi user `sftpuser` untuk hanya memiliki akses ke folder `D:/wadaw` dan hanya bisa menggunakan SFTP.

5. **Buat Pengguna untuk SFTP:**
   - Buat pengguna Windows dengan perintah berikut di Command Prompt (dengan administrator):
     ```bash
     net user sftpuser YourPassword123 /add
     ```
   - Tambahkan pengguna ini ke grup pengguna `SFTP Users` untuk memberikan akses yang dibutuhkan.

6. **Restart Layanan SSH:**
   - Setelah mengubah konfigurasi, restart layanan SSH untuk menerapkan perubahan:
     ```bash
     net stop sshd
     net start sshd
     ```

#### **2. Konfigurasi Windows Server B (192.168.1.3) untuk Mounting Folder via SFTP**
1. **Instal WinSCP atau Dokany:**
   - Di Windows Server B, instal WinSCP (GUI) atau Dokany (untuk mounting secara langsung) yang mendukung koneksi SFTP.

2. **Konfigurasi WinSCP untuk Koneksi SFTP:**
   - Buka WinSCP, pilih *New Site*.
   - Pilih *File Protocol: SFTP*.
   - Isi *Host name* dengan `192.168.1.2` dan *Port number* dengan `4522`.
   - Masukkan username `sftpuser` dan password yang Anda buat sebelumnya.
   - Klik *Advanced* > *Directories* > *Remote directory* dan masukkan path `D:/wadaw`.
   - Simpan konfigurasi dan koneksi.

3. **Mounting Folder secara Otomatis:**
   - WinSCP memungkinkan untuk melakukan "Keep remote directory up to date", namun untuk mounting secara otomatis, Anda bisa menggunakan Dokany atau perangkat lunak lain seperti `sshfs-win`.
   - Jika menggunakan `sshfs-win`, Anda dapat menjalankan perintah berikut di PowerShell (dengan administrator):
     ```powershell
     net use Z: \\sshfs\user@192.168.1.2!4522\D:\wadaw /user:sftpuser YourPassword123
     ```
   - Ini akan mem-mount folder `D:/wadaw` dari Windows Server A ke drive Z: di Windows Server B melalui SFTP.

#### **3. Verifikasi dan Pengujian**
1. **Cek Koneksi:**
   - Pastikan bahwa Anda bisa mengakses `Z:` di Windows Server B dan melihat isi dari `D:/wadaw` di Windows Server A.
  
2. **Troubleshooting:**
   - Jika ada masalah, pastikan firewall di kedua server mengizinkan port 4522, dan cek log SSH di Windows Server A untuk kesalahan.

### Kesimpulan:
Dengan mengikuti langkah-langkah di atas, Anda dapat melakukan sharing folder di jaringan antara dua server Windows melalui SFTP, tanpa menggunakan Samba. Port default untuk SFTP (port 22) telah diubah menjadi 4522 untuk keamanan tambahan. Proses ini melibatkan instalasi OpenSSH di Windows Server A, konfigurasi pengguna dan folder, serta setup koneksi SFTP di Windows Server B.
