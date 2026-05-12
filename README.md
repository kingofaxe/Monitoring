# 🖥️ Monitoring Stack

Stack monitoring lengkap untuk homeserver menggunakan Docker Compose.

## Services

| Service | Port Direct | Via Nginx (`:8099`) | Fungsi |
|---------|------------|---------------------|--------|
| **Grafana** | `:3000` | `/grafana/` | Dashboard & grafik |
| **Prometheus** | `:9090` | `/prometheus/` | Metrics collector |
| **Uptime Kuma** | `:3001` | `/status/` | Status page & alert |
| **cAdvisor** | `:8080` | — | Monitor Docker containers |
| **Node Exporter** | `:9100` | — | Monitor VM resources |
| **SNMP Exporter** | `:9116` | — | Monitor MikroTik & Unifi |
| **Nginx** | `:8099` | — | Reverse proxy |

---

## 🚀 Cara Deploy ke Server Linux

### 1. Upload ke Server

Dari PC Windows, buka PowerShell:

```powershell
# Upload semua file ke server (ganti IP & user)
scp -r C:\Monitoring\* user@IP_SERVER:/opt/web/monitoring/
```

Atau pakai **rsync** dari Git Bash / WSL:

```bash
rsync -avz --exclude '.git' /mnt/c/Monitoring/ user@IP_SERVER:/opt/web/monitoring/
```

### 2. SSH ke Server

```bash
ssh user@IP_SERVER
cd /opt/web/monitoring
```

### 3. Edit Konfigurasi

```bash
# Edit .env — isi password Grafana & IP-IP target
nano .env

# Edit prometheus.yml — ganti IP yang bertanda "GANTI_IP"
nano prometheus/prometheus.yml

# Edit snmp.yml — ganti community string jika perlu
nano snmp/snmp.yml
```

### 4. Jalankan

```bash
docker compose up -d
```

### 5. Cek Status

```bash
# Lihat semua container
docker compose ps

# Lihat logs jika ada error
docker compose logs -f

# Lihat logs service tertentu
docker compose logs -f prometheus
docker compose logs -f grafana
```

---

## 🔍 Akses Setelah Deploy

| URL | Keterangan |
|-----|-----------|
| `http://IP_SERVER:8099` | Dashboard utama (redirect ke Grafana) |
| `http://IP_SERVER:8099/grafana/` | Grafana |
| `http://IP_SERVER:8099/prometheus/` | Prometheus |
| `http://IP_SERVER:8099/status/` | Uptime Kuma |
| `http://IP_SERVER:9090/targets` | Cek target Prometheus |

**Login Grafana:** `admin` / password yang kamu set di `.env`

---

## 📝 File yang Perlu Diedit

| File | Yang Perlu Diisi |
|------|-----------------|
| `.env` | Password Grafana, semua IP target |
| `prometheus/prometheus.yml` | IP yang bertanda `← GANTI_IP` |
| `snmp/snmp.yml` | Community string (jika bukan `public`) |

---

## 🔄 Perintah Berguna

```bash
# Restart semua service
docker compose restart

# Restart satu service
docker compose restart prometheus

# Update image ke versi terbaru
docker compose pull
docker compose up -d

# Hapus semua dan mulai ulang (DATA HILANG!)
docker compose down -v

# Reload config Prometheus tanpa restart
curl -X POST http://localhost:9090/-/reload
```

---

## 📁 Struktur File

```
/opt/web/monitoring/
├── .env                    ← Password & IP (JANGAN commit!)
├── .gitignore
├── docker-compose.yml
├── README.md
├── nginx/
│   └── nginx.conf          ← Reverse proxy config
├── prometheus/
│   ├── prometheus.yml      ← Target scrape config
│   └── alerts.yml          ← Alert rules
└── snmp/
    └── snmp.yml            ← SNMP exporter modules
```
