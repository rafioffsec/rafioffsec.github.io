---
layout: post
title: "Information Gathering - OWASP Amass"
date: 2025-03-28
categories: general
permalink: /posts/information-gathering-owasp-amass/
---

### Apa itu OWASP Amass? 

**OWASP Amass** adalah alat open-source yang digunakan untuk **pengintaian (reconnaissance)** dalam pengujian keamanan dan peretasan. 

Amass dikembangkan oleh OWASP (Open Web Application Security Project) dan dirancang untuk membantu kita dalam mengumpulkan informasi tentang target, memetakan infrastruktur, dan mengidentifikasi potensi titik masuk serangan.  

Amass sering digunakan dalam bug bounty hunting, penetration testing, dan threat intelligence untuk mendapatkan gambaran lengkap tentang infrastruktur target.

### Opsi parameter OWASP Amass

| Opsi | Deskripsi |  
|------|-----------|  
| -active | Mencoba mengambil nama dari sertifikat |  
| -addr value | IP dan rentang IP (misal: 192.168.1.1-254) dipisahkan dengan koma |  
| -asn value | ASN (Autonomous System Numbers) dipisahkan dengan koma (bisa digunakan beberapa kali) |  
| -cidr value | CIDR dipisahkan dengan koma (bisa digunakan beberapa kali) |  
| -config string | Path ke file konfigurasi YAML |  
| -d value | Nama domain dipisahkan dengan koma (bisa digunakan beberapa kali) |  
| -demo | Menyensor output agar cocok untuk demonstrasi |  
| -df value | Path ke file yang berisi root domain |  
| -dir string | Path ke direktori untuk menyimpan output file |  
| -ef string | Path ke file yang menyediakan sumber data yang dikecualikan |  
| -exclude value | Nama sumber data yang akan dikecualikan, dipisahkan dengan koma |  
| -h atau -help | Menampilkan pesan penggunaan program |  
| -if string | Path ke file yang menyediakan sumber data yang akan disertakan |  
| -include value | Nama sumber data yang akan disertakan, dipisahkan dengan koma |  
| -ip | Menampilkan alamat IP dari nama yang ditemukan |  
| -ipv4 | Menampilkan alamat IPv4 dari nama yang ditemukan |  
| -ipv6 | Menampilkan alamat IPv6 dari nama yang ditemukan |  
| -list | Menampilkan informasi tambahan |  
| -log string | Path ke file log untuk mencatat kesalahan |  
| -max-dns-queries int | Jumlah maksimum kueri DNS yang berjalan secara bersamaan |  
| -o string | Path ke file teks untuk menyimpan output dari terminal |  
| -org string | Mencari string yang terkait dengan deskripsi AS (Autonomous System) |  
| -p value | Port yang dipisahkan dengan koma (default: 80, 443) |  
| -r value | IP dari DNS resolver yang lebih disukai (bisa digunakan beberapa kali) |  
| -rf value | Path ke file yang menyediakan daftar DNS resolver yang lebih disukai |  
| -timeout int | Batas waktu dalam menit sebelum enumeration dihentikan |  
| -v | Menampilkan informasi status/debug/troubleshooting |  
| -whois | Semua domain yang diberikan akan diproses melalui reverse WHOIS |

---

### Cara penggunaan OWASP Amass (Basic) 

**1.1 Mencari Subdomain Secara Default**  
```bash  
amass enum -d contoh.com  
```      
- Mencari subdomain dari domain target menggunakan sumber data publik    
- Metode passive reconnaissance   
  
---  
  
**1.2 Menampilkan Alamat IP dari Subdomain**  
```bash  
amass enum -d contoh.com -ip  
```     
- Menampilkan nama subdomain    
- Menampilkan Alamat IPv4 & IPv6
  
---  
  
**1.3 Menyimpan Hasil ke File**  
```bash  
amass enum -d contoh.com -o hasil.txt  
```  
 - digunakan untuk dokumentasi atau analisis lebih lanjut
  
---  
  
**1.4 Passive Enumeration (Tanpa DNS Requests)**  
```bash  
amass enum -d contoh.com -passive  
```  
- Gunakan mode ini jika ingin tetap stealth (tanpa melakukan DNS queries langsung).
  
---  
  
**1.5 Menggunakan File Domain List**  
```bash  
amass enum -df domains.txt  
```  
- File domains.txt berisi daftar domain target:   
```  
example.com  
target.com  
company.net  
```  
- Bisa digunakan untuk multi-target reconnaissance
  
---  
  
### Cara penggunaan OWASP Amass (Advanced) 
  
**2.1 Subdomain Enumeration dengan Brute-force**  
```bash  
amass enum -d contoh.com -brute -w wordlist.txt  
```  
- Mencoba menemukan subdomain tersembunyi dengan brute-force.
- Gunakan wordlist yang baik, seperti: SecLists, Commonspeak2, atau dnsrecon.txt.
  
---  
  
**2.2 Menentukan Resolver Kustom untuk DNS Resolution**  
```bash  
amass enum -d contoh.com -r 8.8.8.8,1.1.1.1 -rf resolvers.txt  
```      
- Keuntunganya dapat menghindari rate-limiting dari DNS resolver bawaan    
- Bisa menggunakan resolver pribadi untuk meningkatkan performa    
  
---  
  
**2.3 Menampilkan Detail ASN & CIDR dari Target**  
```bash  
amass intel -org "Google"  
```  
```bash  
amass intel -asn 15169 -cidr  
```  
- Menampilkan informasi mengenai IP range yang dimiliki oleh target berdasarkan ASN.
  
---  
  
**2.4 Reverse WHOIS Lookup (Mencari Domain Berdasarkan Email)**  
```bash  
amass intel -whois -email admin@target.com  
```  
- Dapat menemukan domain lain yang terhubung dengan email tersebut.
- Bermanfaat untuk OSINT dan footprinting.
  
---  
  
**2.5 Visualisasi Data dalam Bentuk Graph**  
```bash  
amass viz -dir amass_output -d3  
```  
Integrasi dengan:    
- Maltego, Neo4j and Gephi
- Gunakan ini untuk mempermudah analisis data hasil enumeration.
  
---  
  
**2.6 Monitoring Perubahan Infrastruktur**  
```bash  
amass track -dir amass_output -d contoh.com  
```  
- Melacak perubahan subdomain atau infrastruktur dalam jangka waktu tertentu.   
  
---  
  
**2.7 Menjalankan Amass dengan Konfigurasi YAML**  
```bash  
amass enum -d contoh.com -config config.yaml  
```  
- Gunakan konfigurasi khusus untuk mengatur pencarian subdomain secara lebih detail.
  
- **Contoh isi config.yaml**:  

```yaml  
domains:  
  - example.com  
  - test.com  
bruteforce: true  
dns_resolvers:  
  - 8.8.8.8  
  - 1.1.1.1  
output_directory: ./amass_results  
```

---

### Target OWASP Amass

#### 1. Subdomain dan Infrastruktur Web

- **Subdomain Takeover**: Jika Amass menemukan subdomain yang tidak dikonfigurasi dengan benar, bisa terjadi subdomain takeover.
- **Exposed Development or Staging Sites**: Situs pengembangan yang ditemukan bisa memiliki kelemahan keamanan.
- **API Endpoints**: Subdomain API yang ditemukan bisa dieksploitasi jika ada kelemahan seperti kebocoran data atau misconfiguration.

#### 2. Server dan Infrastruktur Jaringan

- **IP Enumeration**: Amass dapat menghubungkan domain ke alamat IP tertentu, membantu dalam menemukan server backend yang mungkin kurang aman.
- **Cloud Resources**: Jika layanan cloud (AWS, GCP, Azure) ditemukan, bisa terjadi eksploitasi terhadap konfigurasi yang buruk.

#### 3. Email dan Karyawan

- **Social Engineering & Phishing**: Jika Amass menemukan email atau informasi organisasi, bisa digunakan untuk serangan phishing atau rekayasa sosial.
- **Credential Stuffing**: Jika akun terkait ditemukan, bisa diuji dengan kredensial yang telah bocor.

#### 4. DNS Misconfiguration

- **Zone Transfer Attack**: Jika konfigurasi DNS tidak aman, seorang penyerang bisa memperoleh seluruh daftar subdomain dengan memanfaatkan data yang dikumpulkan oleh Amass.
- **Typosquatting & Homograph Attacks**: Penyerang bisa mendaftarkan domain mirip dengan subdomain yang ditemukan.

#### 5. Sertifikat SSL dan Teknologi Web

- **SSL Misconfiguration**: Amass bisa menemukan sertifikat SSL yang tidak dikonfigurasi dengan benar, yang bisa digunakan untuk serangan seperti MITM.
- **Framework atau CMS Versioning**: Informasi tentang teknologi yang digunakan bisa membantu penyerang mencari exploit yang sesuai.

