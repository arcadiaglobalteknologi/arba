# Panduan Instalasi Arba

**Arba** adalah AI agent self-improving buatan [Arcadia Global Teknologi](https://arcadiaglobalteknologi.com).  
Panduan ini mencakup semua metode instalasi: one-liner, manual, Docker, Windows, dan Android/Termux.

> **Catatan nama:** perintah yang dijalankan adalah **`arba`**, tapi distribusi Python-nya masih bernama
> **`hermes-agent`** (dijaga untuk kompatibilitas). Jadi file wheel bernama `hermes_agent-….whl`
> dan untuk uninstall gunakan `pipx uninstall hermes-agent`.

---

## Daftar Isi

1. [Prasyarat](#1-prasyarat)
2. [Instalasi Cepat (Linux / macOS / WSL2)](#2-instalasi-cepat-linux--macos--wsl2)
3. [Instalasi Windows Native](#3-instalasi-windows-native)
4. [Instalasi Android / Termux](#4-instalasi-android--termux)
5. [Instalasi Docker](#5-instalasi-docker)
6. [Instalasi Manual (Developer)](#6-instalasi-manual-developer)
7. [Konfigurasi Pertama](#7-konfigurasi-pertama)
8. [Fitur Opsional](#8-fitur-opsional)
9. [Update](#9-update)
10. [Uninstall](#10-uninstall)
11. [Troubleshooting](#11-troubleshooting)

---

## 1. Prasyarat

| Kebutuhan | Versi |
|-----------|-------|
| Python | **3.11, 3.12, atau 3.13** (3.12 direkomendasikan; 3.14 belum didukung) |
| OS | Linux, macOS, Windows 10/11 (native atau WSL2), Android (Termux) |
| Disk | ~500 MB (instalasi dasar) |
| RAM | Minimal 512 MB |

Untuk instalasi one-liner, **uv** dan **git** akan diinstal otomatis oleh installer.

---

## 2. Instalasi Cepat (Linux / macOS / WSL2)

Cara paling mudah — jalankan satu perintah di terminal:

```bash
curl -fsSL https://raw.githubusercontent.com/arcadiaglobalteknologi/arba/main/scripts/install.sh | bash
```

Installer akan otomatis:
- Menginstal **uv** (Python package manager)
- Menginstal **Python 3.11** (jika belum ada)
- Menginstal **Node.js 22** (untuk alat browser)
- Menginstal **ripgrep** dan **ffmpeg**
- Meng-clone repo ke `~/.arba/arba/`
- Membuat virtual environment dan menginstal semua dependensi
- Menambahkan `arba` ke `PATH`

Setelah instalasi selesai:

```bash
source ~/.bashrc      # atau: source ~/.zshrc
arba                  # mulai chatting!
```

### Opsi Installer

```bash
# Lewati tahap yang butuh input pengguna
curl -fsSL ... | bash -s -- --non-interactive

# Hanya instal dependensi tertentu (tanpa clone repo)
curl -fsSL ... | bash -s -- --ensure node,browser,ripgrep

# Tentukan direktori instalasi sendiri
curl -fsSL ... | bash -s -- --dir /opt/arba

# Tentukan direktori data sendiri
curl -fsSL ... | bash -s -- --arba-home /opt/arba-data
```

---

## 3. Instalasi Windows Native

Jalankan di **PowerShell** (tidak perlu hak admin):

```powershell
iex (irm https://raw.githubusercontent.com/arcadiaglobalteknologi/arba/main/scripts/install.ps1)
```

Installer menangani segalanya: uv, Python 3.11, Node.js, ripgrep, ffmpeg, dan **Git Bash portabel**
(MinGit, dipasang ke `%LOCALAPPDATA%\arba\git` — tidak menyentuh Git sistem yang sudah ada).

Setelah instalasi, buka terminal baru lalu jalankan:

```powershell
arba
```

Data dan konfigurasi tersimpan di `%LOCALAPPDATA%\arba\`.

> **Catatan:** Satu-satunya fitur yang masih butuh WSL2 adalah pane chat dashboard berbasis browser
> (menggunakan POSIX PTY). CLI biasa dan gateway berjalan natively.

---

## 4. Instalasi Android / Termux

Di Termux, jalankan installer Linux biasa:

```bash
curl -fsSL https://raw.githubusercontent.com/arcadiaglobalteknologi/arba/main/scripts/install.sh | bash
```

Installer mendeteksi Termux secara otomatis dan menggunakan `.[termux]` extra
(bukan `.[all]` yang memiliki dependensi voice tidak kompatibel dengan Android).

Setelah selesai:

```bash
source ~/.bashrc
arba
```

---

## 5. Instalasi Docker

### Menggunakan Docker Compose (direkomendasikan)

```bash
# Clone repo konfigurasi
git clone https://github.com/arcadiaglobalteknologi/arba.git
cd arba

# Jalankan dengan Docker Compose
docker compose up -d
```

### Menjalankan langsung

```bash
# Pull image terbaru
docker pull arcadiaglobalteknologi/arba:latest

# Jalankan dengan data yang persisten
docker run -it \
  -v "$HOME/.arba:/opt/data" \
  -e ARBA_HOME=/opt/data \
  arcadiaglobalteknologi/arba:latest
```

### Update Docker image

```bash
docker pull arcadiaglobalteknologi/arba:latest
# Kemudian restart container:
docker compose up -d --force-recreate
# atau untuk run manual:
docker run --rm arcadiaglobalteknologi/arba:latest --version
```

---

## 6. Instalasi Manual (Developer)

Gunakan metode ini jika ingin mengembangkan atau memodifikasi Arba.

### Prasyarat manual

Instal Python 3.11–3.13 dan pastikan tersedia di PATH.

Instal **pipx** (opsional, direkomendasikan):

```bash
python3.12 -m pip install --user pipx
python3.12 -m pipx ensurepath    # restart shell setelah ini
```

### Metode A — pipx (satu perintah, global)

```bash
git clone https://github.com/arcadiaglobalteknologi/arba.git
cd arba
pipx install --python python3.12 -e .
```

`arba` kini tersedia global. Flag `-e` = editable, perubahan kode langsung aktif.
Hapus `-e` untuk instalasi statis.

### Metode B — virtualenv + pip

```bash
git clone https://github.com/arcadiaglobalteknologi/arba.git
cd arba

python3.12 -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -e .
```

`arba` tersedia selama venv aktif.

### Metode C — wheel (untuk distribusi ke mesin lain)

**Build** di mesin sumber:

```bash
pipx run --python python3.12 build --wheel
# Menghasilkan: dist/hermes_agent-0.15.1-py3-none-any.whl (~9 MB)
```

**Instal** di mesin tujuan (hanya butuh file `.whl`, tidak perlu seluruh repo):

```bash
pipx install --python python3.12 ./hermes_agent-0.15.1-py3-none-any.whl
```

> CLI dan gateway bekerja langsung dari wheel. TUI dan dashboard memerlukan
> JavaScript bundle yang dibangun terpisah.

### Setup cepat untuk kontributor

```bash
git clone https://github.com/arcadiaglobalteknologi/arba.git
cd arba
./setup-hermes.sh     # instal uv, buat venv, instal .[all], symlink ke ~/.local/bin/arba
./arba                # auto-deteksi venv, tidak perlu source terlebih dahulu
```

Atau secara manual:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
uv venv .venv --python 3.12
source .venv/bin/activate
uv pip install -e ".[all,dev]"
scripts/run_tests.sh
```

---

## 7. Konfigurasi Pertama

Setelah instalasi, jalankan wizard setup untuk mengkonfigurasi provider LLM:

```bash
arba setup
```

Wizard akan memandu memilih provider (OpenRouter, OpenAI, Anthropic, dll.) dan memasukkan API key.

### Perintah dasar

```bash
arba              # Mulai sesi chat interaktif
arba model        # Pilih / ganti provider dan model LLM
arba tools        # Konfigurasi tools yang aktif
arba config set   # Set nilai konfigurasi individual
arba gateway      # Jalankan messaging gateway (Telegram, Discord, dll.)
arba setup        # Jalankan wizard setup penuh
arba update       # Update ke versi terbaru
arba doctor       # Diagnosa masalah instalasi
arba --help       # Daftar semua perintah
```

### Lokasi data

| Platform | Lokasi default |
|----------|---------------|
| Linux / macOS / WSL2 | `~/.arba/` |
| Windows native | `%LOCALAPPDATA%\arba\` |
| Docker | `/opt/data/` (bind-mount dari host) |
| Termux | `~/.arba/` |

Ganti lokasi dengan env var `ARBA_HOME` (atau `HERMES_HOME` untuk kompatibilitas):

```bash
export ARBA_HOME=/data/arba-custom
arba
```

---

## 8. Fitur Opsional

Instalasi dasar sengaja dibuat ringan. Aktifkan fitur tambahan sesuai kebutuhan:

```bash
# Dengan metode pipx
pipx inject hermes-agent "hermes-agent[messaging]"   # Telegram / Discord / Slack / WhatsApp
pipx inject hermes-agent "hermes-agent[voice]"        # Speech-to-text lokal
pipx inject hermes-agent "hermes-agent[anthropic]"    # Provider Anthropic native
pipx inject hermes-agent "hermes-agent[mcp]"          # Server MCP
pipx inject hermes-agent "hermes-agent[web]"          # Dashboard web lokal
pipx inject hermes-agent "hermes-agent[all]"          # Semua fitur (ukuran besar)

# Dengan metode virtualenv
pip install -e ".[messaging]"
pip install -e ".[voice]"
pip install -e ".[all]"
```

### Tools tambahan

```bash
arba tools        # GUI untuk mengaktifkan/menonaktifkan tools
npm install       # Dependensi Node.js untuk alat browser (di folder repo)
```

---

## 9. Update

### Instalasi via one-liner (git)

```bash
arba update
```

### Instalasi via pipx

```bash
pipx upgrade hermes-agent
```

### Instalasi via Docker

```bash
docker pull arcadiaglobalteknologi/arba:latest
docker compose up -d --force-recreate
```

### Instalasi via Homebrew

```bash
brew upgrade hermes-agent
```

---

## 10. Uninstall

```bash
# Instalasi via pipx
pipx uninstall hermes-agent

# Instalasi via virtualenv
# Cukup hapus folder .venv

# Hapus data pengguna (konfigurasi, sesi, memori)
rm -rf ~/.arba

# Windows: hapus data di
# %LOCALAPPDATA%\arba\
```

---

## 11. Troubleshooting

| Gejala | Solusi |
|--------|--------|
| `arba: command not found` | Jalankan `pipx ensurepath` lalu restart shell; atau tambahkan `~/.local/bin` ke `PATH` |
| Error build / wheel saat instalasi | Kemungkinan menggunakan Python 3.14. Gunakan 3.12: tambahkan `--python python3.12` |
| `No module named build` (Metode C) | Gunakan `pipx run build …` (berjalan di env sementara) |
| Windows: perintah tidak ditemukan setelah instalasi | Buka terminal baru untuk memuat PATH yang diperbarui |
| Perlu konfigurasi model | Jalankan `arba setup`, atau `arba model` untuk ganti provider |
| Masalah permission di Linux/macOS | Jangan gunakan `sudo`. Instalasi per-user via pipx atau venv sudah cukup |
| Gateway tidak terhubung ke Telegram/Discord | Jalankan `arba doctor` untuk diagnosa, pastikan API key sudah diset |
| Data tersimpan di lokasi lama `~/.hermes` | Set `ARBA_HOME=~/.hermes` atau biarkan Arba mendeteksi otomatis (backward compat) |
| `arba update` gagal karena bukan git repo | Reinstall via one-liner: `curl -fsSL .../install.sh \| bash` |

---

Untuk panduan lengkap, kunjungi **[arcadiaglobalteknologi.com/docs](https://arcadiaglobalteknologi.com/docs)**.  
Laporkan masalah di **[GitHub Issues](https://github.com/arcadiaglobalteknologi/arba/issues)**.
