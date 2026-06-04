# Panduan Instalasi Arba

**Arba** adalah AI agent self-improving buatan [Arcadia Global Teknologi](https://arcadiaglobalteknologi.com).

> **Catatan nama:** perintah yang dijalankan adalah **`arba`**, tapi distribusi Python-nya bernama
> **`hermes-agent`** (dijaga untuk kompatibilitas). File wheel bernama `hermes_agent-….whl`
> dan untuk uninstall gunakan `pipx uninstall hermes-agent`.

---

## Daftar Isi

1. [Prasyarat](#1-prasyarat)
2. [Instalasi Cepat — Linux / macOS / WSL2](#2-instalasi-cepat-linux--macos--wsl2)
3. [Instalasi dari Folder Lokal (Developer)](#3-instalasi-dari-folder-lokal-developer)
4. [Instalasi Windows](#4-instalasi-windows)
5. [Instalasi Android / Termux](#5-instalasi-android--termux)
6. [Instalasi Docker](#6-instalasi-docker)
7. [Distribusi via Wheel](#7-distribusi-via-wheel)
8. [Konfigurasi Pertama](#8-konfigurasi-pertama)
9. [Fitur Opsional](#9-fitur-opsional)
10. [Update](#10-update)
11. [Uninstall](#11-uninstall)
12. [Troubleshooting](#12-troubleshooting)

---

## 1. Prasyarat

| Kebutuhan | Versi |
|-----------|-------|
| Python | **3.11, 3.12, atau 3.13** (3.12 direkomendasikan; 3.14 belum didukung) |
| OS | Linux, macOS, Windows 10/11 (native atau WSL2), Android (Termux) |
| Disk | ~500 MB |
| RAM | Minimal 512 MB |

Install **uv** terlebih dahulu (package manager yang digunakan Arba):

```bash
# Linux / macOS / WSL2
curl -LsSf https://astral.sh/uv/install.sh | sh
source $HOME/.local/bin/env   # atau restart terminal

# Windows (PowerShell)
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
```

---

## 2. Instalasi Cepat (Linux / macOS / WSL2)

Jalankan satu perintah di terminal:

```bash
curl -fsSL https://raw.githubusercontent.com/arcadiaglobalteknologi/arba/master/arba-agent/scripts/install.sh | bash
```

Installer otomatis menangani: uv, Python 3.11, Node.js 22, ripgrep, ffmpeg, clone repo, venv, dan menambahkan `arba` ke PATH.

Setelah selesai:

```bash
source ~/.bashrc      # atau: source ~/.zshrc
arba                  # mulai chatting!
```

### Opsi installer

```bash
# Lewati input pengguna
curl -fsSL https://raw.githubusercontent.com/arcadiaglobalteknologi/arba/master/arba-agent/scripts/install.sh | bash -s -- --non-interactive

# Hanya instal dependensi tertentu
curl -fsSL https://raw.githubusercontent.com/arcadiaglobalteknologi/arba/master/arba-agent/scripts/install.sh | bash -s -- --ensure node,browser,ripgrep

# Tentukan direktori instalasi
curl -fsSL https://raw.githubusercontent.com/arcadiaglobalteknologi/arba/master/arba-agent/scripts/install.sh | bash -s -- --dir /opt/arba

# Tentukan direktori data
curl -fsSL https://raw.githubusercontent.com/arcadiaglobalteknologi/arba/master/arba-agent/scripts/install.sh | bash -s -- --arba-home /opt/arba-data
```

---

## 3. Instalasi dari Folder Lokal (Developer)

Jika kamu sudah punya folder project, semua metode di bawah dijalankan **dari dalam folder tersebut**.

```bash
cd /path/ke/arba-agent    # sesuaikan dengan lokasi folder kamu
```

---

### Metode A — Setup otomatis (direkomendasikan)

Script `setup-hermes.sh` menangani semua langkah sekaligus: membuat venv, instal dependensi, dan symlink `arba` ke PATH.

```bash
./setup-hermes.sh
```

Setelah selesai, `arba` langsung bisa dijalankan dari mana saja:

```bash
arba
```

> Script ini mensyaratkan `uv` sudah terinstal (lihat [Prasyarat](#1-prasyarat)).

---

### Metode B — uv (cepat, tanpa aktivasi venv)

```bash
# Instal semua dependensi
uv pip install -e ".[all]"

# Atau hanya dependensi inti
uv pip install -e .
```

Jalankan langsung tanpa aktivasi venv:

```bash
uv run arba
```

---

### Metode C — pipx (global, terisolasi)

```bash
pipx install --python python3.12 -e .
```

`arba` kini tersedia global di semua terminal. Flag `-e` = editable — perubahan kode langsung aktif.
Hapus `-e` untuk instalasi statis.

```bash
arba      # langsung bisa dipakai
```

---

### Metode D — virtualenv + pip (klasik)

```bash
python3.12 -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -e .
```

`arba` tersedia selama venv aktif. Untuk aktivasi otomatis, tambahkan ke `.bashrc`/`.zshrc`:

```bash
# Tambahkan ke ~/.bashrc atau ~/.zshrc
export PATH="/path/ke/arba-agent/.venv/bin:$PATH"
```

---

## 4. Instalasi Windows

### Windows native (PowerShell) — one-liner

Jalankan di **PowerShell** (tidak perlu hak admin):

```powershell
iex (irm https://raw.githubusercontent.com/arcadiaglobalteknologi/arba/master/arba-agent/scripts/install.ps1)
```

Installer menangani segalanya: uv, Python 3.11, Node.js, ripgrep, ffmpeg, dan Git Bash portabel
(dipasang ke `%LOCALAPPDATA%\arba\git` — tidak menyentuh Git sistem yang ada).

Setelah instalasi, buka terminal baru lalu jalankan:

```powershell
arba
```

Data dan konfigurasi tersimpan di `%LOCALAPPDATA%\arba\`.

### Windows dari folder lokal (PowerShell)

```powershell
cd C:\path\ke\arba-agent

python -m venv .venv
.venv\Scripts\activate
pip install -e .
```

Atau dengan uv:

```powershell
uv pip install -e ".[all]"
uv run arba
```

> `ARBA_HOME` bisa digunakan untuk mengganti lokasi data default.

### WSL2

Ikuti one-liner Linux di atas di terminal WSL2. Data tersimpan di `~/.arba/`.

---

## 5. Instalasi Android / Termux

Di Termux, jalankan installer Linux biasa:

```bash
curl -fsSL https://raw.githubusercontent.com/arcadiaglobalteknologi/arba/master/arba-agent/scripts/install.sh | bash
```

Installer mendeteksi Termux otomatis dan menggunakan `.[termux]` extra.

```bash
source ~/.bashrc
arba
```

---

## 6. Instalasi Docker

Build image dari Dockerfile yang sudah ada di folder project:

```bash
# Build image
docker build -t arba:latest .

# Jalankan dengan data persisten
docker run -it \
  -v "$HOME/.arba:/opt/data" \
  -e ARBA_HOME=/opt/data \
  arba:latest

# Atau dengan Docker Compose
docker compose up -d
```

---

## 7. Distribusi via Wheel

Untuk mendistribusikan Arba ke mesin lain tanpa perlu menyalin seluruh folder:

**Build** di mesin sumber:

```bash
uv build --wheel
# atau:
pipx run --python python3.12 build --wheel

# Menghasilkan: dist/hermes_agent-0.15.1-py3-none-any.whl
```

**Kirim** file `.whl` ke mesin tujuan, lalu **instal**:

```bash
pipx install --python python3.12 ./hermes_agent-0.15.1-py3-none-any.whl
# atau:
pip install ./hermes_agent-0.15.1-py3-none-any.whl
```

> CLI dan gateway berjalan langsung dari wheel. Dashboard TUI memerlukan JavaScript bundle yang dibangun terpisah via `npm install`.

---

## 8. Konfigurasi Pertama

Setelah instalasi, jalankan wizard setup:

```bash
arba setup
```

Wizard memandu kamu memilih provider LLM (OpenRouter, OpenAI, Anthropic, dll.) dan memasukkan API key.

### Perintah dasar

```bash
arba              # Mulai sesi chat interaktif
arba model        # Pilih / ganti provider dan model LLM
arba tools        # Konfigurasi tools yang aktif
arba config set   # Set nilai konfigurasi individual
arba gateway      # Jalankan messaging gateway (Telegram, Discord, dll.)
arba setup        # Jalankan wizard setup penuh
arba doctor       # Diagnosa masalah instalasi
arba --help       # Daftar semua perintah
```

### Lokasi data

| Platform | Lokasi default |
|----------|---------------|
| Linux / macOS / WSL2 | `~/.arba/` |
| Windows native | `%LOCALAPPDATA%\arba\` |
| Docker | `/opt/data/` (bind-mount dari host) |

Ganti lokasi dengan env var:

```bash
export ARBA_HOME=/data/arba-custom   # Linux / macOS
set ARBA_HOME=D:\arba-data           # Windows CMD
$env:ARBA_HOME = "D:\arba-data"      # Windows PowerShell
```

> `HERMES_HOME` juga masih didukung untuk kompatibilitas ke belakang.

---

## 9. Fitur Opsional

Instalasi dasar sengaja ringan. Aktifkan fitur tambahan sesuai kebutuhan:

```bash
# Dengan uv
uv pip install -e ".[messaging]"    # Telegram / Discord / Slack / WhatsApp
uv pip install -e ".[voice]"        # Speech-to-text lokal
uv pip install -e ".[anthropic]"    # Provider Anthropic native
uv pip install -e ".[mcp]"          # Server MCP
uv pip install -e ".[web]"          # Dashboard web lokal
uv pip install -e ".[all]"          # Semua fitur (ukuran besar)

# Dengan pip (venv aktif)
pip install -e ".[messaging]"
pip install -e ".[all]"

# Dengan pipx
pipx inject hermes-agent "hermes-agent[messaging]"
pipx inject hermes-agent "hermes-agent[all]"
```

### Tools tambahan

```bash
npm install       # Dependensi Node.js untuk alat browser (jalankan di folder project)
arba tools        # GUI untuk mengaktifkan / menonaktifkan tools
```

---

## 10. Update

### Instalasi editable (Metode A / B / C / D)

Cukup `git pull` di folder project — karena instalasi editable, perubahan langsung aktif tanpa reinstall:

```bash
git pull
# Jika ada dependensi baru:
uv pip install -e ".[all]"
```

Atau gunakan perintah bawaan Arba:

```bash
arba update
```

### Instalasi via wheel

Build ulang wheel dan reinstall:

```bash
uv build --wheel
pipx install --force ./dist/hermes_agent-*.whl
```

### Instalasi via Docker

```bash
docker build -t arba:latest .
docker compose up -d --force-recreate
```

---

## 11. Uninstall

```bash
# Instalasi via pipx
pipx uninstall hermes-agent

# Instalasi via virtualenv — hapus folder venv
rm -rf .venv

# Hapus data pengguna (konfigurasi, sesi, memori)
rm -rf ~/.arba

# Windows: hapus data di %LOCALAPPDATA%\arba\
```

---

## 12. Troubleshooting

| Gejala | Solusi |
|--------|--------|
| `arba: command not found` | Pastikan `~/.local/bin` ada di `PATH`; jalankan `pipx ensurepath` lalu restart terminal |
| Error Python 3.14 | Gunakan Python 3.12: `uv venv .venv --python 3.12` |
| `No module named build` | Gunakan `pipx run build …` atau `uv build --wheel` |
| Windows: perintah tidak ditemukan | Buka terminal baru setelah instalasi untuk memuat PATH |
| Perlu konfigurasi model | Jalankan `arba setup` atau `arba model` |
| Permission error Linux/macOS | Jangan gunakan `sudo` — instalasi per-user sudah cukup |
| Gateway tidak terhubung | Jalankan `arba doctor`, cek API key di `arba config` |
| Data di `~/.hermes` (instalasi lama) | Set `ARBA_HOME=~/.hermes` atau biarkan Arba mendeteksi otomatis |
| `uv` tidak ditemukan | Instal uv: `curl -LsSf https://astral.sh/uv/install.sh \| sh` |
| Dependensi bentrok | Gunakan `uv pip install` — uv memiliki resolver yang lebih ketat dari pip |

---

Untuk panduan lengkap, kunjungi **[arcadiaglobalteknologi.com/docs](https://arcadiaglobalteknologi.com/docs)**.  
Laporkan masalah di **[GitHub Issues](https://github.com/arcadiaglobalteknologi/arba/issues)**.
