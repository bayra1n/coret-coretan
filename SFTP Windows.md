
# SFTP WINDOWS SERVER


Ya, Anda dapat menggunakan SFTP (Secure File Transfer Protocol) untuk mengelola file secara aman antara Windows Server A dan Windows Server B. Namun, perlu diperhatikan bahwa SFTP secara tradisional digunakan untuk transfer file dan bukan untuk mounting file system seperti NFS atau SMB. Meskipun demikian, ada beberapa cara untuk mencapai tujuan yang Anda inginkan, yaitu mengakses dan menggunakan file yang di-host di Windows Server A dari Windows Server B melalui SFTP. Berikut adalah cara melakukannya:

### 1. **Menyediakan SFTP Server pada Windows Server A**

Pertama, Anda perlu menyiapkan SFTP server pada Windows Server A.

#### Langkah-langkah:
1. **Instal SFTP Server:**
   - Anda bisa menggunakan software SFTP server seperti OpenSSH (yang sudah termasuk dalam versi Windows yang lebih baru), FileZilla Server, atau Bitvise SSH Server.
   - Contoh: Menggunakan OpenSSH yang sudah tersedia pada Windows 10/Server 2019 atau lebih baru:
     - Buka *Settings* > *Apps* > *Optional Features*.
     - Cari dan tambahkan *OpenSSH Server*.
     - Setelah instalasi, jalankan layanan OpenSSH Server dari *Services.msc*.

2. **Konfigurasi Pengguna:**
   - Tentukan pengguna yang memiliki akses ke SFTP. Anda dapat menggunakan pengguna Windows yang ada, atau membuat akun khusus untuk SFTP.

3. **Akses SFTP:**
   - Uji akses SFTP dari komputer lain dengan menggunakan client SFTP seperti WinSCP atau FileZilla untuk memastikan bahwa SFTP server berfungsi dengan baik.

### 2. **Mengakses SFTP dari Windows Server B**

Setelah SFTP server di Windows Server A siap, Anda bisa mengakses file dari Windows Server B. Untuk "mounting" SFTP sebagai drive, Anda akan membutuhkan software tambahan, karena Windows tidak mendukung mounting SFTP secara native.

#### Gunakan Dokan atau WinFsp + SSHFS untuk Mounting SFTP:

**Dokan SSHFS:**
1. **Install Dokan Library**: Dokan Library memungkinkan Anda untuk mengemulasi file system menggunakan SFTP.
   - Unduh dan instal Dokan dari [situs resmi Dokan](https://dokan-dev.github.io/).
   - Setelah itu, instal SSHFS-Win yang memungkinkan Anda untuk mounting SFTP sebagai drive di Windows.
     - SSHFS-Win dapat diunduh dari [GitHub SSHFS-Win](https://github.com/billziss-gh/sshfs-win).

2. **Mounting SFTP:**
   - Buka Command Prompt dengan hak administrator.
   - Gunakan perintah berikut untuk mounting:
     ```bash
     sshfs-win svc \user@hostname:/remote_path X:
     ```
   - `user@hostname:/remote_path` adalah kredensial dan jalur ke SFTP server Anda di Windows Server A.
   - `X:` adalah huruf drive yang akan digunakan pada Windows Server B.

**WinFsp + SSHFS:**
1. **Install WinFsp**: WinFsp adalah file system for Windows yang memungkinkan implementasi file system di ruang pengguna.
   - Unduh dan instal WinFsp dari [situs resmi WinFsp](http://www.secfs.net/winfsp/).
   
2. **Install SSHFS**:
   - Instal SSHFS yang merupakan file system client menggunakan SFTP.
   - Gunakan command berikut untuk mounting:
     ```bash
     sshfs user@hostname:/remote_path X:
     ```
   - Sama seperti sebelumnya, ganti `user@hostname:/remote_path` dan `X:` sesuai dengan konfigurasi Anda.

### 3. **Mengakses Folder yang di-Mount di Windows Server B**
Setelah SFTP di-mount sebagai drive di Windows Server B, Anda dapat mengaksesnya seperti drive lokal lainnya di Windows Explorer, Command Prompt, atau aplikasi lain yang memerlukan akses ke file.

### **Kesimpulan:**
Meskipun Windows tidak mendukung mounting SFTP secara native, Anda dapat menggunakan alat seperti Dokan SSHFS atau WinFsp + SSHFS untuk melakukan mounting SFTP server pada Windows Server B. Ini memungkinkan Anda mengakses file di Windows Server A melalui SFTP seolah-olah mereka adalah bagian dari file system lokal di Windows Server B. Metode ini memberikan keamanan tambahan melalui enkripsi SFTP dan memungkinkan manajemen file yang efisien antar server.
