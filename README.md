# 🌐 Web Server Project: Instalasi & Konfigurasi Apache2 di Debian



Proyek ini mendokumentasikan langkah-langkah instalasi, konfigurasi, hingga _deployment_ proyek HTML pada Apache2 Web Server di lingkungan Debian.



---



## Berkenalan dengan Apache Web Server



**Apache2 Web Server** adalah server web yang **kuat, fleksibel, dan mudah digunakan**. Server ini dapat dijalankan di berbagai sistem operasi dan mendukung banyak modul tambahan untuk keamanan, kinerja, serta pengelolaan situs.



Meskipun Apache2 sedikit lebih berat dibandingkan server lain (seperti Nginx) saat menangani trafik tinggi, keunggulannya terletak pada **kemudahan konfigurasi** dan **dukungan komunitas yang luas**, menjadikannya pilihan praktis dan efisien, terutama bagi pemula.



---



## 1. ⚙️ Tahapan Instalasi Apache, PHP, dan SSL



Berikut adalah langkah-langkah detail dari penyiapan server hingga uji coba fitur:



### A. Menyiapkan Debian Server 🐧



1.  Siapkan **server Debian** yang sudah memiliki **IP Address** dan dapat diakses melalui jaringan LAN.

2.  Atur **Repository** agar dapat digunakan untuk instalasi perangkat lunak.

3.  Uji koneksi ke server menggunakan **SSH** (misalnya CMD) dan **WinSCP** untuk memastikan akses sudah berfungsi.



### B. Instalasi Apache Web Server 🌐



1.  **Login dan Update Paket:**

    ```bash

    apt update && apt upgrade

    ```

2.  **Instal Apache:**

    ```bash

    apt install apache2

    ```

3.  **Aktifkan dan Pastikan Berjalan:**

    ```bash

    systemctl enable apache2

    systemctl start apache2

    systemctl status apache2

    ```

    

4.  **Uji Coba Apache:** Akses dari browser: `http://ip-server`



### C. Instalasi PHP 🐘



1.  **Instal PHP Dasar:**

    ```bash

    apt install php

    ```

2.  **Instalasi Extension PHP Tambahan:**

    ```bash

    apt install php-common php-xml php-curl php-zip php-gd php-mbstring php-intl php-json php-soap php-mysql

    ```

3.  **Pastikan PHP Berjalan:**

    * Buat file uji: `nano /var/www/html/info.php`

    * Tambahkan script: `<?php phpinfo(); ?>`

4.  **Uji Coba PHP:** Akses dari browser: `http://ip-server/info.php`

    



### D. Menambahkan SSL Self-Signed Certificate 🔐



1.  **Instal OpenSSL dan Aktifkan Modul SSL:**

    ```bash

    apt install openssl

    a2enmod ssl

    ```

2.  **Buat Folder dan Sertifikat:**

    ```bash

    mkdir /etc/apache2/ssl

    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/selfsigned.key -out /etc/apache2/ssl/selfsigned.crt

    ```

    (Isi detail sertifikat saat diminta: ID, Jawa Barat, SMKN 1 Soreang, server.local, dll.)



3.  **Konfigurasi Virtual Host HTTPS:**

    * Salin file konfigurasi:

        ```bash

        cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/000-default-ssl.conf

        ```

    * Edit file baru (`nano /etc/apache2/sites-available/000-default-ssl.conf`) dan tambahkan blok `<VirtualHost *:443>` dengan konfigurasi SSL:

        ```apache

        <VirtualHost *:443>

            ServerAdmin admin@localhost

            DocumentRoot /var/www/html

            ServerName server.local



            SSLEngine on

            SSLCertificateFile /etc/apache2/ssl/selfsigned.crt

            SSLCertificateKeyFile /etc/apache2/ssl/selfsigned.key



            # ... Konfigurasi Direktori Lain ...

        </VirtualHost>

        ```



4.  **Aktifkan HTTPS dan Modul Rewrite:**

    ```bash

    a2ensite 000-default-ssl.conf

    a2enmod rewrite

    systemctl reload apache2

    ```

5.  **Uji Coba SSL:** Akses dari browser: `https://ip-server`



### E. Redirect HTTP ke HTTPS (Opsional) 🔗



Ini disarankan hanya jika server menggunakan IP statis dan DNS berfungsi baik.



1.  Edit file konfigurasi HTTP:

    ```bash

    nano /etc/apache2/sites-available/000-default.conf

    ```

2.  Tambahkan perintah _redirect_ di dalam `<VirtualHost *:80>`:

    ```apache

    <VirtualHost *:80>

        # ... Konfigurasi Awal ...

        

        # Redirect semua akses HTTP ke HTTPS

        Redirect "/" "[https://server.local/](https://server.local/)"



        # ... Konfigurasi Log Lain ...

    </VirtualHost>

    ```

3.  _Reload_ Apache:

    ```bash

    systemctl reload apache2

    ```



---



## 2. 📝 Kelebihan & Kekurangan Web Server Apache



## ⭐ Kelebihan & Kekurangan Apache Web Server

| ⭐ **Kelebihan Apache** | ❗ **Kekurangan Apache** |
|-------------------------|---------------------------|
| **Mudah dikonfigurasi** (user-friendly, cocok untuk pemula). | **Kurang cepat** untuk trafik sangat besar (lebih boros resource). |
| Mendukung file **`.htaccess`** (sangat fleksibel). | **Butuh lebih banyak RAM** (setiap proses membutuhkan memori). |
| **Kompatibel** dengan banyak aplikasi web (WordPress, Laravel, dll.). | **Tidak se-efisien NGINX** untuk file statis. |
| **Modul sangat banyak** (`mod_ssl`, `mod_rewrite`, dll.). | Konfigurasi bisa **rumit pada skala besar**. |
| **Komunitas besar** & dokumentasi lengkap. | Performa **kalah dari OLS** untuk website PHP modern. |
| **Stabil dan mature** (sejak 1995). | - |
| Cocok untuk server **kecil–menengah**. | - |




---



## 3. 🧩 Cara Membuat dan Mengupload Project HTML



### A. Membuat Project HTML 🌱



Saya memulai dengan membuat proyek HTML sederhana dengan struktur dasar:



Proyek ini didesain menggunakan **HTML dan CSS** di komputer lokal, memastikan semua elemen (teks, gambar, navigasi) tersusun rapi.



### B. Menyiapkan File untuk Di-upload 🚀



Setelah desain selesai, saya memastikan:

* Semua file sudah berada dalam satu folder.

* File utama **`index.html`** berada di luar (root) folder proyek.

* Tidak ada _path_ gambar yang salah.

* (Opsional) Folder dikompres menggunakan **ZIP** agar lebih mudah di-upload.



### C. Mengupload Website ke Server Apache 📤



Lokasi utama file website di Apache adalah: `/var/www/html`.



#### 🔹 Cara 1: Upload via WinSCP (Paling mudah)

1.  Koneksi WinSCP ke `ip-server` (menggunakan user `root` atau user Debian).

2.  Masuk ke direktori `/var/www/html`.

3.  Hapus file default Apache: `rm index.html`

4.  **Upload** seluruh file HTML, CSS, dan folder proyek ke `/var/www/html`.

    

5.  Akses website melalui `http://ip-server` atau `https://ip-server`.



#### 🔹 Cara 2: Upload via Terminal (SSH)

1.  Gunakan **SCP** (Secure Copy Protocol) untuk memindahkan file:

    ```bash

    scp -r project-website/* root@ip-server:/var/www/html

    ```

2.  Pastikan _permission_ file benar (Apache biasanya berjalan dengan user `www-data`):

    ```bash

    chown -R www-data:www-data /var/www/html

    chmod -R 755 /var/www/html

    ```



### D. Uji Website 🎉

Setelah _upload_, lakukan pengecekan via browser untuk memastikan:

* Halaman utama tampil dengan benar.

* Semua gambar, CSS, dan JavaScript ter-load.

* Semua tautan navigasi bekerja.



---



## 4. ⚠️ Kendala dan Solusi yang Dialami


| 🐞 Kendala (Masalah) | 🔧 Solusi (Penyelesaian) |
|----------------------|--------------------------|
| Apache tidak bisa diakses dari browser setelah instalasi. | Cek status layanan (`systemctl status apache2`). Pastikan firewall (`iptables`/`ufw`) telah mengizinkan akses pada **Port 80 (HTTP)**. |
| PHP info page tidak muncul atau malah diunduh. | Pastikan modul PHP untuk Apache sudah terinstal (misalnya `libapache2-mod-php`). **Restart Apache** setelah instalasi modul. |
| Sertifikat SSL (self-signed) memunculkan peringatan **"Not Secure"**. | Ini normal untuk sertifikat self-signed. Bisa diabaikan atau gunakan sertifikat valid dari **Let's Encrypt**. |
| Gambar atau CSS tidak muncul setelah upload HTML. | Periksa kembali **path** file di HTML (`<img src="...">`). Pastikan **permission** benar: `chown -R www-data:www-data /var/www/html`. |
| Redirect HTTP → HTTPS tidak berfungsi. | Pastikan **mod_rewrite** aktif (`a2enmod rewrite`) dan lakukan `systemctl reload apache2`. Cek juga **ServerName** sudah benar. |




 
