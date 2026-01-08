# IPX - IP Ban Manager for Linux

Script bash untuk mengelola banned dan unbanned IP address di Linux menggunakan iptables dengan fitur auto-unban dan monitoring status.

## 🚀 Fitur

- ✅ **Ban IP Permanen** - Block IP tanpa batas waktu
- ⏰ **Ban IP Temporary** - Block IP dengan durasi tertentu (dalam menit)
- 🔄 **Auto-Unban** - Otomatis unbanned setelah waktu yang ditentukan
- 📋 **List Banned IPs** - Tampilkan semua IP yang ter-banned dengan detail
- 🔍 **Check Status** - Cek status IP tertentu (banned/tidak)
- 📝 **Activity Log** - Catat semua aktivitas ban/unban
- ✔️ **IP Validation** - Validasi format IP address
- 🛡️ **Duplicate Check** - Cegah banned IP yang sudah ter-banned

## 📋 Requirements

- Linux OS (Ubuntu, Debian, CentOS, dll)
- iptables
- Root access (sudo)
- crontab (untuk auto-unban)

## 📥 Instalasi

### Metode 1: Download dan Install

```bash
# Download script
wget https://raw.githubusercontent.com/your-repo/ipx/main/ipx -O /tmp/ipx

# Pindahkan ke direktori system
sudo mv /tmp/ipx /usr/local/bin/ipx

# Berikan permission executable
sudo chmod +x /usr/local/bin/ipx

# Verifikasi instalasi
ipx -h
```

### Metode 2: Manual Install

```bash
# Buat file script
sudo nano /usr/local/bin/ipx

# Copy paste script ke dalam file
# Save dengan Ctrl+X, Y, Enter

# Berikan permission executable
sudo chmod +x /usr/local/bin/ipx
```

## 📖 Cara Penggunaan

### Syntax Dasar

```bash
ipx -ip <IP_ADDRESS> -banned [-time <MENIT>]
ipx -ip <IP_ADDRESS> -unbanned
ipx -ip <IP_ADDRESS> -status
ipx -list
```

### 1. Ban IP Permanen

Block IP tanpa batas waktu:

```bash
sudo ipx -ip 192.168.1.100 -banned
```

Output:
```
✅ IP 192.168.1.100 berhasil di-banned secara PERMANEN
```

### 2. Ban IP Temporary

Block IP dengan durasi tertentu (dalam menit):

```bash
# Ban selama 60 menit (1 jam)
sudo ipx -ip 192.168.1.100 -banned -time 60

# Ban selama 1440 menit (24 jam / 1 hari)
sudo ipx -ip 10.0.0.50 -banned -time 1440

# Ban selama 10080 menit (7 hari / 1 minggu)
sudo ipx -ip 172.16.0.200 -banned -time 10080
```

Output:
```
✅ IP 192.168.1.100 berhasil di-banned selama 60 menit
📅 IP akan di-unbanned otomatis pada: 2026-01-08 15:30:00
```

### 3. Unbanned IP

Hapus banned dari IP address:

```bash
sudo ipx -ip 192.168.1.100 -unbanned
```

Output:
```
✅ IP 192.168.1.100 berhasil di-unbanned
```

### 4. Cek Status IP

Cek apakah IP ter-banned atau tidak:

```bash
sudo ipx -ip 192.168.1.100 -status
```

Output untuk IP yang ter-banned:
```
🔍 Status IP: 192.168.1.100
================================
Status: 🔴 BANNED
Tipe: ⏰ TEMPORARY
Auto-unban: Hari 08, Bulan 01, Jam 15:30

📜 History:
2026-01-08 13:30:15 - BANNED: 192.168.1.100
================================
```

Output untuk IP yang tidak ter-banned:
```
🔍 Status IP: 8.8.8.8
================================
Status: ✅ TIDAK BANNED (Normal/Allowed)
================================
```

### 5. Lihat Daftar IP Banned

Tampilkan semua IP yang ter-banned dengan detail:

```bash
sudo ipx -list
```

Output:
```
📋 Daftar IP yang ter-banned:
================================
  🔴 192.168.1.100
     ⏰ Tipe: TEMPORARY
     📅 Auto-unban: Hari 08, Bulan 01, Jam 15:30
     📝 Log: 2026-01-08 13:30:15 - BANNED: 192.168.1.100

  🔴 10.0.0.50
     ⏰ Tipe: PERMANEN
     📝 Log: 2026-01-08 12:15:30 - BANNED: 10.0.0.50

================================
Total: 2 IP ter-banned
```

### 6. Bantuan

Tampilkan panduan penggunaan:

```bash
ipx -h
# atau
ipx --help
```

## 🔧 Konfigurasi

### File Lokasi

- **Script**: `/usr/local/bin/ipx`
- **Log File**: `/var/log/ipx_banned.log`
- **Temp File**: `/tmp/ipx_unban_jobs`

### Crontab

Script menggunakan crontab untuk auto-unban. Untuk melihat scheduled unban:

```bash
sudo crontab -l | grep "Auto-unban"
```

## 💡 Contoh Use Case

### Scenario 1: Block Brute Force Attack

```bash
# Deteksi IP yang melakukan brute force
# Ban temporary selama 2 jam (120 menit)
sudo ipx -ip 203.0.113.50 -banned -time 120

# Cek status
sudo ipx -ip 203.0.113.50 -status
```

### Scenario 2: Permanent Ban untuk IP Malicious

```bash
# Ban permanen untuk IP yang berbahaya
sudo ipx -ip 198.51.100.25 -banned

# Verifikasi dengan list
sudo ipx -list
```

### Scenario 3: Unban Manual

```bash
# Cek dulu apakah IP ter-banned
sudo ipx -ip 192.168.1.100 -status

# Jika ter-banned, lakukan unban manual
sudo ipx -ip 192.168.1.100 -unbanned

# Verifikasi status lagi
sudo ipx -ip 192.168.1.100 -status
```

### Scenario 4: Monitoring Multiple IPs

```bash
# Lihat semua IP yang ter-banned
sudo ipx -list

# Cek status specific IPs
sudo ipx -ip 192.168.1.100 -status
sudo ipx -ip 10.0.0.50 -status
sudo ipx -ip 172.16.0.200 -status
```

## ⚠️ Catatan Penting

1. **Root Access Required**: Script harus dijalankan dengan `sudo` atau sebagai root user
2. **Iptables**: Script menggunakan iptables untuk blocking, pastikan iptables terinstall
3. **Firewall Rules**: Ban akan hilang jika iptables di-restart, kecuali sudah disimpan
4. **Crontab**: Auto-unban menggunakan crontab user root

### Menyimpan Rules Iptables (Agar Persisten)

Untuk Ubuntu/Debian:
```bash
sudo apt-get install iptables-persistent
sudo netfilter-persistent save
```

Untuk CentOS/RHEL:
```bash
sudo service iptables save
```

## 🐛 Troubleshooting

### Error: "Script ini harus dijalankan sebagai root"

**Solusi**: Gunakan `sudo`
```bash
sudo ipx -ip 192.168.1.100 -banned
```

### Error: "Format IP address tidak valid"

**Solusi**: Pastikan format IP benar (contoh: 192.168.1.100)
```bash
# ❌ Salah
sudo ipx -ip 192.168.1 -banned

# ✅ Benar
sudo ipx -ip 192.168.1.100 -banned
```

### IP sudah ter-banned tapi masih bisa akses

**Solusi**: Cek iptables rules
```bash
sudo iptables -L INPUT -n -v
```

### Auto-unban tidak bekerja

**Solusi**: Cek crontab
```bash
sudo crontab -l
```

## 📊 Monitoring dan Logging

### Melihat Log Aktivitas

```bash
# Lihat semua log
sudo cat /var/log/ipx_banned.log

# Lihat log terakhir
sudo tail -f /var/log/ipx_banned.log

# Cari log specific IP
sudo grep "192.168.1.100" /var/log/ipx_banned.log
```

### Melihat Rules Iptables

```bash
# Lihat semua rules
sudo iptables -L INPUT -n -v

# Lihat hanya DROP rules
sudo iptables -L INPUT -n -v | grep DROP
```

## 🤝 Contributing

Kontribusi sangat diterima! Silakan:

1. Fork repository
2. Buat branch feature (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add some AmazingFeature'`)
4. Push ke branch (`git push origin feature/AmazingFeature`)
5. Buat Pull Request

## 📝 License

MIT License - Silakan gunakan secara bebas untuk proyek personal atau komersial.
