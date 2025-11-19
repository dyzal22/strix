FROM kalilinux/kali-rolling

ENV DEBIAN_FRONTEND=noninteractive
ENV PIP_BREAK_SYSTEM_PACKAGES=1
# Host localhost aman untuk container internal
ENV CAIDO_HOST=127.0.0.1
ENV CAIDO_PORT=8080

# 1. Install Base System & Tools
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl wget git vim nano unzip tar \
    ca-certificates gnupg lsb-release \
    build-essential software-properties-common \
    python3 python3-pip python3-dev python3-venv python3-setuptools \
    golang-go net-tools dnsutils whois jq parallel ripgrep grep \
    less procps htop iproute2 iputils-ping netcat-traditional \
    nmap ncat ndiff sqlmap nuclei subfinder naabu ffuf \
    nodejs npm pipx libcap2-bin tmux gdb \
    libnss3 libnspr4 libdbus-1-3 libatk1.0-0 libatk-bridge2.0-0 libcups2 libdrm2 libatspi2.0-0 \
    libxcomposite1 libxdamage1 libxfixes3 libxrandr2 libgbm1 libxkbcommon0 libpango-1.0-0 libcairo2 libasound2t64 \
    fonts-unifont fonts-noto-color-emoji fonts-freefont-ttf fonts-dejavu-core ttf-bitstream-vera \
    libnss3-tools && \
    rm -rf /var/lib/apt/lists/*

# 2. Download Caido (Link Langsung - Versi v0.53.0)
# Kita pakai link GitHub Release langsung agar tidak error pencarian
RUN wget -O caido.tar.gz https://github.com/caido/caido/releases/download/v0.53.0/caido-cli-v0.53.0-linux-x86_64.tar.gz && \
    tar -xzf caido.tar.gz && \
    # Cari file binary (biasanya bernama caido-cli) lalu rename jadi caido dan pindahkan
    find . -type f -name "caido-cli" -o -name "caido" | head -n 1 | xargs -I {} mv {} /usr/local/bin/caido && \
    chmod +x /usr/local/bin/caido && \
    rm -rf caido.tar.gz *caido*

# 3. Setup Project
WORKDIR /workspace
COPY . /workspace

# 4. Install Python Dependencies
RUN pip3 install --no-cache-dir .

# 5. Setup Entrypoint
COPY containers/docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh
RUN chmod +x /usr/local/bin/docker-entrypoint.sh

ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh"]
CMD ["strix"]
