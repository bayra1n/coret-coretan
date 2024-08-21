
# SFTP WINDOWS SERVER


Ya, Anda bisa menggunakan SFTP untuk mengakses dan mengelola folder dari Windows Server A di Windows Server B tanpa menggunakan Samba. Namun, perlu dicatat bahwa SFTP (Secure File Transfer Protocol) biasanya digunakan untuk transfer file dan tidak mendukung mounting direktori secara langsung seperti NFS atau SMB. Tetapi, Anda bisa menggunakan tools tertentu yang memungkinkan SFTP untuk diakses seolah-olah seperti drive atau folder yang di-mount pada sistem Windows, misalnya menggunakan *WinSCP* atau *SSHFS-Win*.

### Langkah-langkah Konfigurasi

#### 1. **Setup SFTP Server di Windows Server A**

Pertama, kita akan mengonfigurasi Windows Server A untuk menjalankan SFTP server. Anda bisa menggunakan OpenSSH Server yang tersedia di Windows.

1. **Instal OpenSSH Server:**
   - Buka *Settings* > *Apps* > *Optional Features*.
   - Scroll ke bawah dan klik *Add a feature*.
   - Cari *OpenSSH Server* dan klik *Install*.

2. **Konfigurasi SFTP Server:**
   - Setelah instalasi, buka *Services* (Anda bisa mengetik "Services" di Start menu).
   - Temukan layanan *OpenSSH SSH Server*, klik kanan, dan pilih *Start*. Anda juga bisa mengatur layanan ini agar otomatis mulai dengan memilih *Properties* dan mengatur *Startup type* ke *Automatic*.
   
3. **Setup Direktori SFTP:**
   - Buat direktori pengguna khusus untuk SFTP atau gunakan yang sudah ada. Misalnya, kita akan menggunakan `D:/wadaw/` sebagai direktori SFTP.
   - Pastikan pengguna yang mengakses SFTP memiliki izin yang tepat pada folder tersebut.
   - Anda bisa mengonfigurasi `sshd_config` (konfigurasi OpenSSH) untuk menentukan direktori mana yang bisa diakses oleh pengguna tertentu. File konfigurasi ini biasanya ada di `C:\ProgramData\ssh\sshd_config`.

    ```text
    Match User [username]
      ChrootDirectory D:/wadaw
      ForceCommand internal-sftp
      AllowTcpForwarding no
      X11Forwarding no
    ```

   - Restart layanan *OpenSSH SSH Server* agar perubahan berlaku:
     ```cmd
     net stop sshd
     net start sshd
     ```

#### 2. **Mount SFTP sebagai Drive di Windows Server B**

Sekarang, kita akan mengonfigurasi Windows Server B untuk mengakses dan memount folder yang tersedia di Windows Server A sebagai drive melalui SFTP.

1. **Instal SSHFS-Win:**
   - *SSHFS-Win* adalah driver yang memungkinkan Anda untuk mount direktori yang diakses melalui SFTP sebagai drive di Windows.
   - Download dan instal *SSHFS-Win* dari [GitHub repository SSHFS-Win](https://github.com/billziss-gh/sshfs-win/releases).

2. **Mount Folder dari Windows Server A di Windows Server B:**
   - Buka Command Prompt atau PowerShell dengan hak administrator.
   - Gunakan perintah `net use` dengan `sshfs` untuk mounting folder dari SFTP:
     ```bash
     net use Z: \\sshfs\username@ServerA:/wadaw
     ```
     Gantilah `username` dengan nama pengguna SFTP yang sudah diatur di Windows Server A dan `ServerA` dengan alamat IP atau hostname dari Windows Server A.

3. **Konfigurasi Credential:**
   - Anda mungkin akan diminta untuk memasukkan password untuk pengguna SFTP. Anda bisa menyimpan credential ini agar tidak perlu memasukkannya setiap kali melakukan mounting.

4. **Akses Folder yang Di-mount:**
   - Setelah di-mount, folder dari Windows Server A akan muncul sebagai drive `Z:` di Windows Server B. Anda bisa mengaksesnya seperti drive biasa melalui File Explorer.

5. **Otomatisasi Mounting (Opsional):**
   - Anda bisa membuat script batch untuk otomatis mounting saat startup. Buat file `.bat` dengan perintah `net use` di atas dan tambahkan ke *Task Scheduler* agar berjalan saat login.

### Kesimpulan

Dengan menggunakan SFTP dan tool seperti SSHFS-Win, Anda bisa mengakses dan "mount" folder dari Windows Server A di Windows Server B tanpa menggunakan Samba. Proses ini memerlukan pengaturan OpenSSH Server di Windows Server A dan menggunakan SSHFS-Win di Windows Server B untuk melakukan mounting folder sebagai drive.

Metode ini tidak menggunakan Samba dan tetap aman karena SFTP menggunakan enkripsi SSH untuk melindungi data yang dikirimkan di antara server. Pastikan juga bahwa Anda mengelola izin dengan baik pada folder dan pengguna untuk menjaga keamanan data Anda.
