FROM kalilinux/kali-rolling

ENV DEBIAN_FRONTEND=noninteractive
ENV PIP_BREAK_SYSTEM_PACKAGES=1
ENV CAIDO_HOST=127.0.0.1
ENV CAIDO_PORT=8080

# 1. Install Base System & Python
# Kita butuh python3 dan library standar untuk script download di bawah
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl wget git unzip tar \
    ca-certificates gnupg lsb-release \
    build-essential software-properties-common \
    python3 python3-pip python3-dev python3-venv python3-setuptools \
    golang-go net-tools dnsutils whois jq \
    nmap ncat sqlmap nuclei subfinder naabu ffuf \
    nodejs npm pipx libcap2-bin && \
    rm -rf /var/lib/apt/lists/*

# 2. DOWNLOAD CAIDO (Metode Python - Anti 404)
# Script ini akan mencari aset bernama *linux*x86_64*.tar.gz di rilis terbaru
RUN echo "Mencari link download Caido terbaru..." && \
    python3 -c "import urllib.request, json; \
    url = 'https://api.github.com/repos/caido/caido/releases/latest'; \
    req = urllib.request.Request(url, headers={'User-Agent': 'Mozilla/5.0'}); \
    r = urllib.request.urlopen(req); \
    data = json.load(r); \
    link = next(a['browser_download_url'] for a in data['assets'] if 'linux' in a['name'] and 'x86_64' in a['name'] and 'tar.gz' in a['name']); \
    print(link)" > caido_link.txt && \
    \
    echo "Mendownload dari: $(cat caido_link.txt)" && \
    wget -O caido.tar.gz -i caido_link.txt && \
    \
    echo "Mengekstrak..." && \
    tar -xzf caido.tar.gz && \
    # Cari file binary hasil ekstrak & pindahkan
    find . -type f \( -name "caido-cli" -o -name "caido" \) -exec mv {} /usr/local/bin/caido \; && \
    chmod +x /usr/local/bin/caido && \
    rm -rf caido.tar.gz caido_link.txt

# 3. Install Browser Dependencies (Layer terpisah agar cache aman)
RUN apt-get update && apt-get install -y --no-install-recommends \
    libnss3 libnspr4 libdbus-1-3 libatk1.0-0 libatk-bridge2.0-0 libcups2 libdrm2 \
    libxcomposite1 libxdamage1 libxfixes3 libxrandr2 libgbm1 libxkbcommon0 libpango-1.0-0 libcairo2 libasound2t64 \
    fonts-unifont fonts-noto-color-emoji fonts-freefont-ttf fonts-dejavu-core ttf-bitstream-vera \
    libnss3-tools && \
    rm -rf /var/lib/apt/lists/*

# 4. Setup Project
WORKDIR /workspace
COPY . /workspace

# Install Python Dependencies
RUN pip3 install --no-cache-dir .

# Entrypoint
COPY containers/docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh
RUN chmod +x /usr/local/bin/docker-entrypoint.sh

ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh"]
CMD ["strix"]
