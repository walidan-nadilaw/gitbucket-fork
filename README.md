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

  - Dashboard.
<img width="735" height="373" alt="image" src="https://github.com/user-attachments/assets/05bc5a38-f118-4403-a446-5092bb4a84b9" />

Penjelasan = Dashboard berfungsi sebagai halaman beranda utama setelah pengguna masuk ke platform. Fitur ini menyajikan tinjauan umum mengenai aktivitas terkini, menyertakan News Feed yang menampilkan pembaruan dari repositori yang diikuti. Pengguna memanfaatkannya sebagai pusat navigasi untuk memantau aktivitas keseluruhan akun dan mengakses cepat repositori yang baru saja diperbarui. Anda bisa menelusuri fitur lainnya ketika berada disini sebab seluruh fitur telah terpresentasi dan terlihat dalam ui dashboard.

  - File.
<img width="735" height="373" alt="image" src="https://github.com/user-attachments/assets/05bc5a38-f118-4403-a446-5092bb4a84b9" />

Penjelasan = Files adalah tampilan utama di dalam repositori yang menunjukkan hierarki folder dan file dari kode sumber proyek pada branch tertentu. Fungsi utamanya adalah menyajikan status kode terbaru dan commit terakhir yang dianggap relevan. Pengguna menggunakannya untuk menelusuri isi proyek secara detail, memeriksa struktur kode, mengamati history perubahan file, serta melihat daftar file. Ini bisa di akses pada menu file.

  - Branches.
<img width="730" height="380" alt="image" src="https://github.com/user-attachments/assets/f6815385-eb1a-4689-b02f-abe49d17b2e3" />

Penjelasan =  Fitur Branches mengelola semua jalur pengembangan independen dalam proyek. Fungsinya memastikan stabilitas kode utama dengan mengisolasi atau menjaga pekerjaan fitur atau perbaikan bug yang sedang dilakukan atau sedang berlangsung. Pengguna membuat branch baru untuk melakukan pekerjaan tanpa mengganggu kode utama, serta membandingkan dan mengelola perkembangan task/pekerjaan.

  - Releases.
  <img width="749" height="353" alt="image" src="https://github.com/user-attachments/assets/30107764-bc0f-46d5-9860-2bb570d72b86" />

Penjelasan = Releases digunakan untuk menandai secara resmi versi stabil dan teruji dari perangkat lunak, biasanya dihubungkan dengan tag Git tertentu. Fungsinya adalah menyediakan titik sejarah yang terdefinisi dengan baik dan siap didistribusikan kepada pengguna akhir atau sistem lainnya. Pengguna memanfaatkan fitur ini untuk melacak sejarah versi produk dan menyediakan paket unduhan sumber kode yang relevan.

  - Issues.
  <img width="1887" height="935" alt="image" src="https://github.com/user-attachments/assets/c65ea7e2-b0d7-41a2-9c32-0bc0d247a053" />

Penjelasan = Issues Merupakan sistem terstruktur untuk mencatat, mengelola, dan mendiskusikan bug, permintaan fitur, serta tugas yang perlu diselesaikan. Fungsi utamanya adalah terletak pada menyediakan pusat terpadu untuk mengatur dan lalu memprioritaskan semua pekerjaan yang diperlukan proyek. Pengguna menggunakannya untuk melaporkan masalah baru, memberikan label, menetapkan penanggung jawab, dan melacak kemajuan solusi.

  - Pull Request.
  <img width="1869" height="995" alt="image" src="https://github.com/user-attachments/assets/57a14f23-a398-4644-ab09-669b45102f4d" />
  
Penjelasan = Pull Requests (PR) adalah fitur penting di GitBucket yang memungkinkan pengguna mengusulkan dan mengajukan perubahan kode dari satu branch ke branch lain (misalnya, master). Fungsi utamanya adalah memastikan kualitas kode tetap terjaga karena PR mewajibkan anggota tim melakukan tinjauan kode (code review) secara menyeluruh sebelum perubahan diizinkan untuk digabungkan. Selain itu, fitur ini sangat memfasilitasi kolaborasi karena semua diskusi, komentar, dan revisi yang terkait dengan usulan perubahan kode tersimpan secara terpusat dan transparan di dalam satu tempat.
 

## Pembahasan

- **Kelebihan dan Kekurangan GitBucket**
    - **Kelebihan**
        1. **Mudah diinstall dan dijalankan**<br>
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
    | Aspek                     | **GitHub**                                                  | **GitBucket**                                      | **Gitea**                                                          | **GitLab**                                             |
    | ------------------------- | ----------------------------------------------------------- | -------------------------------------------------- | ------------------------------------------------------------------ | ------------------------------------------------------ |
    | **Lisensi**               | Proprietary (gratis & berbayar)                             | Open Source (Apache 2.0)                           | Open Source (MIT)                                                  | Open Source (MIT untuk CE, EE berbayar)                |
    | **Bahasa & Runtime**      | Ruby, Go, C, dll. (cloud-hosted)                            | Scala (JVM, butuh Java 17)                         | Go (binary ringan, cross-platform)                                 | Ruby + Go (self-host)                                  |
    | **Hosting**               | Cloud (github.com), GitHub Enterprise Server (self-host)    | Self-host (via `gitbucket.war`)                    | Cloud (try.gitea.io) & self-host                                   | Cloud (gitlab.com) & self-host                         |
    | **Kemudahan Instalasi**   | Tidak bisa self-host gratis kecuali pakai GitHub Enterprise | Sangat mudah (`java -jar gitbucket.war`)           | Sangat mudah (1 binary Go, sangat ringan)                          | Medium (butuh resource besar, Docker biasanya dipakai) |
    | **Fitur Repositori**      | Lengkap: private/public repo, Git LFS, Actions              | Private/public repo, Git LFS, basic repo viewer    | Private/public repo, Git LFS, basic repo viewer                    | Lengkap: private/public repo, Git LFS, CI/CD           |
    | **Fitur Kolaborasi**      | Issues, PR, Projects, Wiki, Discussions                     | Issues, PR, Wiki, basic timeline                   | Issues, PR, Wiki, basic boards                                     | Issues, MR, Wiki, Boards, Epics, Roadmap               |
    | **CI/CD**                 | GitHub Actions (native, powerful)                           | Tidak ada bawaan (harus pakai Jenkins atau plugin) | Tidak ada bawaan (pakai Drone CI atau eksternal)                   | GitLab CI/CD (native, sangat lengkap)                  |
    | **Integrasi**             | Ribuan apps & marketplace                                   | Jenkins, Slack, LDAP, plugin terbatas              | LDAP, OAuth, plugin sederhana                                      | Banyak integrasi DevOps, Docker Registry, Kubernetes   |
    | **Ekosistem & Komunitas** | Sangat besar, standar industri                              | Relatif kecil                                      | Sedang berkembang, populer di komunitas open source kecil/menengah | Sangat besar, dipakai enterprise & DevOps              |
    | **Skalabilitas**          | Enterprise-grade, sangat besar                              | Cocok untuk tim kecil–menengah                     | Cocok untuk tim kecil–menengah                                     | Enterprise-grade, support cluster                      |

    - **Ringkasan**
        - GitHub: terbaik untuk open source & integrasi luas (tapi self-host berbayar).
        - GitBucket: ringan, mudah install, bagus untuk tim kecil yang butuh mirip GitHub.
        - Gitea: sangat ringan, mudah, cocok untuk komunitas kecil/organisasi dengan resource terbatas.
        - GitLab: cocok untuk DevOps & enterprise dengan CI/CD bawaan.

## Referensi

1. [Tentang GitBucket](https://gitbucket.github.io/) - GitBucket
2. [Repository Gitbucket pada Github](https://github.com/gitbucket/gitbucket) - GitBucket
3. [Tentang Azure](https://azure.microsoft.com/id-id/products/virtual-machines) - Microsoft
4. [Cara Menambah Kontributor pada Azure](https://learn.microsoft.com/en-us/azure/ai-services/luis/luis-how-to-collaborate) - Microsoft
5. [Cara Menginstall GitBucket pada Ubuntu/Debian Linux](https://computingforgeeks.com/how-to-install-gitbucket-on-ubuntu/?amp=) - Computing for Geeks
6. [Cara Install & Konfigurasi Nginx pada Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-20-04) - Digital Ocean
7. [Referensi Git](https://git-scm.com/docs) - Git
8. [Praktikum Komunikasi Data dan Jaringan Komputer 2025/2026](https://github.com/auriza/komdat-lab/blob/master/README.md) - auriza
9. [ChatGPT](https://chatgpt.com/)
10. [Gemini](https://gemini.google.com/app)


