FROM kalilinux/kali-rolling

# Set environment agar tidak ada dialog interaktif saat install
ENV DEBIAN_FRONTEND=noninteractive

# Update repo kali linux
RUN apt-get update

# TAHAP 1: Install Base System & Python (Cicilan 1)
# Kita pecah agar build tidak timeout di satu layer yang berat
RUN apt-get install -y --no-install-recommends \
    wget curl git vim nano unzip tar \
    ca-certificates gnupg lsb-release \
    build-essential software-properties-common \
    gcc libc6-dev pkg-config libpcap-dev libssl-dev \
    python3 python3-pip python3-dev python3-venv python3-setuptools && \
    rm -rf /var/lib/apt/lists/*

# TAHAP 2: Install Go & Network Tools (Cicilan 2)
RUN apt-get update && apt-get install -y --no-install-recommends \
    golang-go \
    net-tools dnsutils whois \
    jq parallel ripgrep grep \
    less procps htop \
    iproute2 iputils-ping netcat-traditional \
    nmap ncat ndiff \
    sqlmap nuclei subfinder naabu ffuf && \
    rm -rf /var/lib/apt/lists/*

# TAHAP 3: Install Browser & dependencies lainnya (Cicilan 3)
# Perhatikan: libasound2 sudah diganti jadi libasound2t64
RUN apt-get update && apt-get install -y --no-install-recommends \
    nodejs npm pipx \
    libcap2-bin tmux gdb \
    libnss3 libnspr4 libdbus-1-3 libatk1.0-0 libatk-bridge2.0-0 libcups2 libdrm2 libatspi2.0-0 \
    libxcomposite1 libxdamage1 libxfixes3 libxrandr2 libgbm1 libxkbcommon0 libpango-1.0-0 libcairo2 libasound2t64 \
    fonts-unifont fonts-noto-color-emoji fonts-freefont-ttf fonts-dejavu-core ttf-bitstream-vera \
    libnss3-tools && \
    rm -rf /var/lib/apt/lists/*

# Setup working directory
WORKDIR /workspace

# Copy source code
COPY . /workspace

# Install Python Dependencies
# Menggunakan pip dengan timeout lebih lama agar tidak gagal saat download lib besar
RUN pip3 install --no-cache-dir --upgrade pip setuptools wheel && \
    pip3 install --no-cache-dir -r pyproject.toml || \
    pip3 install --no-cache-dir .

# Setup Entrypoint
COPY containers/docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh
RUN chmod +x /usr/local/bin/docker-entrypoint.sh

ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh"]
CMD ["strix"]
