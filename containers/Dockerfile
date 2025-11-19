# --- TAHAP 1: SUMBER CAIDO ---
FROM caido/caido:latest AS source

# --- TAHAP 2: PENCARI CAIDO ---
FROM alpine:latest AS finder
COPY --from=source /usr/bin /tmp/bin
COPY --from=source /usr/local/bin /tmp/local_bin
RUN find /tmp -type f \( -name "caido" -o -name "caido-cli" \) -exec cp {} /caido_final \; && \
    chmod +x /caido_final

# --- TAHAP 3: PENDOWNLOAD DOCKER (Baru!) ---
# Kita download Docker Client manual biar pasti ada filenya
FROM alpine:latest AS docker_downloader
RUN apk add --no-cache curl tar
# Download Docker Client Static Binary (Versi Stabil)
RUN curl -fsSL https://download.docker.com/linux/static/stable/x86_64/docker-24.0.7.tgz -o docker.tgz && \
    tar -xzf docker.tgz && \
    mv docker/docker /docker_binary && \
    chmod +x /docker_binary

# --- TAHAP 4: UTAMA (Kali Linux) ---
FROM kalilinux/kali-rolling

ENV DEBIAN_FRONTEND=noninteractive
ENV PIP_BREAK_SYSTEM_PACKAGES=1
ENV CAIDO_HOST=127.0.0.1
ENV CAIDO_PORT=8080

# 1. Install Base System
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl wget git unzip tar \
    ca-certificates gnupg lsb-release \
    build-essential software-properties-common \
    python3 python3-pip python3-dev python3-venv python3-setuptools && \
    rm -rf /var/lib/apt/lists/*

# 2. Install Hacking Tools
RUN apt-get update && apt-get install -y --no-install-recommends \
    golang-go net-tools dnsutils whois jq \
    nmap ncat sqlmap nuclei subfinder naabu ffuf \
    nodejs npm pipx libcap2-bin && \
    rm -rf /var/lib/apt/lists/*

# 3. Install Browser Support
RUN apt-get update && apt-get install -y --no-install-recommends \
    libnss3 libnspr4 libdbus-1-3 libatk1.0-0 libatk-bridge2.0-0 libcups2 libdrm2 \
    libxcomposite1 libxdamage1 libxfixes3 libxrandr2 libgbm1 libxkbcommon0 libpango-1.0-0 libcairo2 libasound2t64 \
    fonts-unifont fonts-noto-color-emoji fonts-freefont-ttf fonts-dejavu-core ttf-bitstream-vera \
    libnss3-tools && \
    rm -rf /var/lib/apt/lists/*

# 4. COPY Binary Caido
COPY --from=finder /caido_final /usr/local/bin/caido

# 5. COPY Binary Docker (SOLUSI MASALAH KAMU)
# Kita taruh langsung di /usr/local/bin supaya pasti ketemu
COPY --from=docker_downloader /docker_binary /usr/local/bin/docker

# 6. Setup Project
WORKDIR /workspace
COPY . /workspace

# 7. Install Python Dependencies
RUN pip3 install --no-cache-dir .

# 8. Entrypoint
COPY containers/docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh
RUN chmod +x /usr/local/bin/docker-entrypoint.sh

ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh"]
CMD ["strix"]
