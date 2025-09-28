GitBucket [![Gitter chat](https://badges.gitter.im/gitbucket/gitbucket.svg)](https://gitter.im/gitbucket/gitbucket) [![build](https://github.com/gitbucket/gitbucket/actions/workflows/build.yml/badge.svg)](https://github.com/gitbucket/gitbucket/actions/workflows/build.yml) [![gitbucket Scala version support](https://index.scala-lang.org/gitbucket/gitbucket/gitbucket/latest-by-scala-version.svg)](https://index.scala-lang.org/gitbucket/gitbucket/gitbucket) [![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://github.com/gitbucket/gitbucket/blob/master/LICENSE)
=========

## Sekilas Tentang

GitBucket adalah sebuah platform web untuk Git yang dibangun dengan Scala, dirancang untuk memberikan pengalaman mirip GitHub dengan kemudahan instalasi dan fleksibilitas tinggi.

**✨ Fitur Utama**

- 🚀 Instalasi mudah dan siap digunakan
- 🎨 Antarmuka intuitif dan ramah pengguna
- 🔌 Sistem plug-in untuk memperluas fungsionalitas
- 🔄 Kompatibel dengan API GitHub
- 🔒 Mendukung repository publik maupun privat (akses via http/https dan ssh)
- 📂 GitLFS support untuk file besar
- 👀 Repository viewer lengkap dengan online file editor
- 📌 Manajemen proyek dengan Issues, Pull Requests, dan Wiki
- 🕑 Activity timeline & email notifications
- 👥 Manajemen akun dan grup, termasuk integrasi LDAP


![GitBucket](https://gitbucket.github.io/img/screenshots/screenshot-repository_viewer.png)

## Instalasi

_**1. Persiapan Server**_

Pastikan sudah ada:

* **Java 11+**
* Akses root / sudo
* VPS dengan IP publik

Update sistem:

``` 
sudo apt update && sudo apt upgrade -y
```

Install Java:

``` 
sudo apt install openjdk-11-jre -y
```


_**2. Buat User Khusus untuk GitBucket**_

``` 
sudo adduser --system --group --home /opt/gitbucket gitbucket
```


_**3. Download GitBucket**_

``` 
cd /opt/gitbucket
sudo wget https://github.com/gitbucket/gitbucket/releases/download/4.39.0/gitbucket.war
sudo chown gitbucket:gitbucket gitbucket.war
```


_**4. Buat Systemd Service**_

Buat file service:

``` 
sudo nano /etc/systemd/system/gitbucket.service
```

Isi dengan:

```
[Unit]
Description=GitBucket Service
After=network.target

[Service]
WorkingDirectory=/opt/gitbucket
ExecStart=/usr/bin/java -Xms128m -Xmx256m -jar gitbucket.war
User=gitbucket
Group=gitbucket
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Reload & enable service:

```
sudo systemctl daemon-reexec
sudo systemctl enable gitbucket
sudo systemctl start gitbucket
```

Cek status:

``` 
sudo systemctl status gitbucket
```


_**5. Akses GitBucket**_

Secara default, GitBucket berjalan di port `8080`.
Coba akses di browser:

```
http://<IP-VPS>:8080
```

Jika memakai domain, buat subdomain (misal `gitbucket.example.com`) → arahkan ke IP VPS via DNS `A Record`.


_**6. Setup Reverse Proxy dengan Nginx**_

Install Nginx:

``` 
sudo apt install nginx -y
```

Buat konfigurasi:

``` 
sudo nano /etc/nginx/sites-available/gitbucket.conf
```

Isi:

```
server {
    listen 80;
    server_name gitbucket.example.com;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Aktifkan config:

``` 
sudo ln -s /etc/nginx/sites-available/gitbucket.conf /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

Coba akses:

```
http://gitbucket.example.com
```


_**7. Tambahkan HTTPS (Opsional tapi Disarankan)**_

Gunakan **Certbot** untuk SSL:

``` 
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d gitbucket.example.com
```

Pilih opsi redirect → selesai.
Sekarang akses dengan:

```
https://gitbucket.example.com
```


## Cara Pemakaian

- Tampilan aplikasi web
- Fungsi-fungsi utama
- Isi dengan data real/dummy (jangan kosongan) dan sertakan beberapa screenshot


## Pembahasan

- **Kelebihan dan Kekurangan GitBucket**
    - **Kelebihan**
        1. **Mudah diinstall dan dijalankan (SEMENTARA)**<br>
            Cukup install gitbucket war lalu menjalankan `java -jar gitbucket.war` di terminal dan gitbucket sudah bisa dijalankan dengan `http://[hostname]:8080/`.
        2. **UI Intuitif dan menyerupai Github**<br>
            Memudahkan pengguna baru untuk adaptasi dan mengekplorasi GitBucket.
        3. **Ekstensibilitas tinggi dengan plugin**<br>
            Ada plugin resmi yaitu gist, emoji, pages, dan email notifications dan plugin komunitas seperti desktop notification, draw.io, markdown enhanced, Kanban Board, dan lainnya.
        4. **Kompatibel dengan Github API**<br>
            Banyak tool pihak ketiga dapat bekerja tanpa banyak penyesuaian.

    - **Kekurangan**
        1. **Bergantung pada Java (JVM)**<br>
            Membutuhkan Java 17 dan tidak bisa jalan di Java versi lama. Hal ini menyebabkan GitBucket lebih berat dari kompetitornya
        2. **Kurang kuat untuk skala besar**<br>
            Lebih cocok untuk tim kecil/menengah bukan untuk deployment enterprise dengan ribuan user dan repositori.
        3. **Ketergantungan pada H2 database default**<br>
            Migrasi database antar versi bisa rumit karena tidak otomatis.
        4. **Tidak mendukung Jakarta EE**<br>
            Masih terbatas pada Servlet 3.0, sehingga tidak bisa di-deploy di beberapa server Java modern tanpa workaround.

- **Perbandingan dengan aplikasi sejenis**
    | Aspek                     | **GitHub**                                                  | **GitLab**                                             | **GitBucket**                                      | **Gitea**                                                          |
    | ------------------------- | ----------------------------------------------------------- | ------------------------------------------------------ | -------------------------------------------------- | ------------------------------------------------------------------ |
    | **Lisensi**               | Proprietary (gratis & berbayar)                             | Open Source (MIT untuk CE, EE berbayar)                | Open Source (Apache 2.0)                           | Open Source (MIT)                                                  |
    | **Bahasa & Runtime**      | Ruby, Go, C, dll. (cloud-hosted)                            | Ruby + Go (self-host)                                  | Scala (JVM, butuh Java 17)                         | Go (binary ringan, cross-platform)                                 |
    | **Hosting**               | Cloud (github.com), GitHub Enterprise Server (self-host)    | Cloud (gitlab.com) & self-host                         | Self-host (via `gitbucket.war`)                    | Cloud (try.gitea.io) & self-host                                   |
    | **Kemudahan Instalasi**   | Tidak bisa self-host gratis kecuali pakai GitHub Enterprise | Medium (butuh resource besar, Docker biasanya dipakai) | Sangat mudah (`java -jar gitbucket.war`)           | Sangat mudah (1 binary Go, sangat ringan)                          |
    | **Fitur Repositori**      | Lengkap: private/public repo, Git LFS, Actions              | Lengkap: private/public repo, Git LFS, CI/CD           | Private/public repo, Git LFS, basic repo viewer    | Private/public repo, Git LFS, basic repo viewer                    |
    | **Fitur Kolaborasi**      | Issues, PR, Projects, Wiki, Discussions                     | Issues, MR, Wiki, Boards, Epics, Roadmap               | Issues, PR, Wiki, basic timeline                   | Issues, PR, Wiki, basic boards                                     |
    | **CI/CD**                 | GitHub Actions (native, powerful)                           | GitLab CI/CD (native, sangat lengkap)                  | Tidak ada bawaan (harus pakai Jenkins atau plugin) | Tidak ada bawaan (pakai Drone CI atau eksternal)                   |
    | **Integrasi**             | Ribuan apps & marketplace                                   | Banyak integrasi DevOps, Docker Registry, Kubernetes   | Jenkins, Slack, LDAP, plugin terbatas              | LDAP, OAuth, plugin sederhana                                      |
    | **Ekosistem & Komunitas** | Sangat besar, standar industri                              | Sangat besar, dipakai enterprise & DevOps              | Relatif kecil                                      | Sedang berkembang, populer di komunitas open source kecil/menengah |
    | **Skalabilitas**          | Enterprise-grade, sangat besar                              | Enterprise-grade, support cluster                      | Cocok untuk tim kecil–menengah                     | Cocok untuk tim kecil–menengah                                     |
    | **Kelebihan**             | - Fitur paling lengkap                                      |                                                        |                                                    |                                                                    |




## Referensi

Cantumkan tiap sumber informasi yang anda pakai.

