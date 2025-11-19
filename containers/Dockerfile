# --- TAHAP 1: DOWNLOADER (Pakai Alpine biar cepat & ringan) ---
FROM alpine:latest AS downloader

WORKDIR /tmp
# Install tools download dasar
RUN apk add --no-cache curl tar findutils

# Download Caido v0.53.0 (Link Langsung)
RUN curl -L -o caido.tar.gz https://github.com/caido/caido/releases/download/v0.53.0/caido-cli-v0.53.0-linux-x86_64.tar.gz && \
    tar -xzf caido.tar.gz && \
    # Cari file binary (namanya caido-cli atau caido), lalu ubah jadi 'caido'
    find . -type f \( -name "caido-cli" -o -name "caido" \) -exec mv {} caido \; && \
    chmod +x caido

# --- TAHAP 2: MAIN IMAGE (Kali Linux) ---
FROM kalilinux/kali-rolling

ENV DEBIAN_FRONTEND=noninteractive
ENV PIP_BREAK_SYSTEM_PACKAGES=1
ENV CAIDO_HOST=127.0.0.1
ENV CAIDO_PORT=8080

# Checkpoint 1: Base Tools
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl wget git unzip tar \
    ca-certificates gnupg lsb-release \
    build-essential software-properties-common \
    python3 python3-pip python3-dev python3-venv python3-setuptools && \
    rm -rf /var/lib/apt/lists/*

# Checkpoint 2: Hacking Tools (Optimized)
RUN apt-get update && apt-get install -y --no-install-recommends \
    golang-go net-tools dnsutils whois jq \
    nmap ncat sqlmap nuclei subfinder naabu ffuf \
    nodejs npm pipx libcap2-bin && \
    rm -rf /var/lib/apt/lists/*

# Checkpoint 3: Browser Support
RUN apt-get update && apt-get install -y --no-install-recommends \
    libnss3 libnspr4 libdbus-1-3 libatk1.0-0 libatk-bridge2.0-0 libcups2 libdrm2 \
    libxcomposite1 libxdamage1 libxfixes3 libxrandr2 libgbm1 libxkbcommon0 libpango-1.0-0 libcairo2 libasound2t64 \
    fonts-unifont fonts-noto-color-emoji fonts-freefont-ttf fonts-dejavu-core ttf-bitstream-vera \
    libnss3-tools && \
    rm -rf /var/lib/apt/lists/*

# COPY Binary Caido dari Tahap 1 (Pasti ada & Path benar)
COPY --from=downloader /tmp/caido /usr/local/bin/caido

# Setup Project
WORKDIR /workspace
COPY . /workspace

# Install Dependencies
RUN pip3 install --no-cache-dir .

# Entrypoint
COPY containers/docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh
RUN chmod +x /usr/local/bin/docker-entrypoint.sh

ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh"]
CMD ["strix"]
