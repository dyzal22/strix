FROM kalilinux/kali-rolling

ENV DEBIAN_FRONTEND=noninteractive
ENV PIP_BREAK_SYSTEM_PACKAGES=1

# 1. Install Base System & Tools
RUN apt-get update && apt-get install -y --no-install-recommends \
    wget curl git vim nano unzip tar \
    ca-certificates gnupg lsb-release \
    build-essential software-properties-common \
    gcc libc6-dev pkg-config libpcap-dev libssl-dev \
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

# 2. Setup Caido (MANUAL INSTALL)
# Kita download manual supaya tidak gagal saat runtime
RUN wget https://storage.googleapis.com/caido-releases/v0.41.0/caido-linux-x86_64-v0.41.0.tar.gz -O caido.tar.gz && \
    tar -xzf caido.tar.gz && \
    mv caido /usr/local/bin/caido && \
    chmod +x /usr/local/bin/caido && \
    rm caido.tar.gz

# 3. Setup Project
WORKDIR /workspace
COPY . /workspace

# 4. Install Python Apps
RUN pip3 install --no-cache-dir .

# 5. Setup Entrypoint
COPY containers/docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh
RUN chmod +x /usr/local/bin/docker-entrypoint.sh

ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh"]
CMD ["strix"]
