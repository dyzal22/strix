# TAHAP 1: Ambil Caido dari Image Resmi (Supaya gak perlu download manual)
FROM caido/caido:latest AS caido_source

# TAHAP 2: Build Image Utama
FROM kalilinux/kali-rolling

ENV DEBIAN_FRONTEND=noninteractive
ENV PIP_BREAK_SYSTEM_PACKAGES=1
ENV CAIDO_HOST=127.0.0.1
ENV CAIDO_PORT=8080

# --- CHECKPOINT 1: Base System (Ringan) ---
# Kita install tools dasar dulu
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl wget git unzip tar \
    ca-certificates gnupg lsb-release \
    build-essential software-properties-common \
    python3 python3-pip python3-dev python3-venv python3-setuptools && \
    rm -rf /var/lib/apt/lists/*

# --- CHECKPOINT 2: Hacking Tools (Berat) ---
# Dipisah supaya kalau timeout, tidak perlu ulang dari nol.
# Saya menghapus tools sampah (vim, nano, man-db, fonts) biar lebih cepat/kecil.
RUN apt-get update && apt-get install -y --no-install-recommends \
    golang-go net-tools dnsutils whois jq \
    nmap ncat sqlmap nuclei subfinder naabu ffuf \
    nodejs npm pipx libcap2-bin && \
    rm -rf /var/lib/apt/lists/*

# --- CHECKPOINT 3: Browser Deps (Sedang) ---
# Khusus dependencies untuk menjalankan browser headless
RUN apt-get update && apt-get install -y --no-install-recommends \
    libnss3 libnspr4 libdbus-1-3 libatk1.0-0 libatk-bridge2.0-0 libcups2 libdrm2 \
    libxcomposite1 libxdamage1 libxfixes3 libxrandr2 libgbm1 libxkbcommon0 libpango-1.0-0 libcairo2 libasound2t64 && \
    rm -rf /var/lib/apt/lists/*

# Copy Binary Caido dari image sebelah (Anti Gagal Download)
COPY --from=caido_source /usr/bin/caido /usr/local/bin/caido
RUN chmod +x /usr/local/bin/caido

# Setup Project
WORKDIR /workspace
COPY . /workspace

# Install Python Dependencies
RUN pip3 install --no-cache-dir .

# Setup Entrypoint
COPY containers/docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh
RUN chmod +x /usr/local/bin/docker-entrypoint.sh

ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh"]
CMD ["strix"]
