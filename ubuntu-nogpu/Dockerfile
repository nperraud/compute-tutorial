FROM ubuntu:latest

# Install dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    cmake \
    git \
    wget \
    unzip \
    htop \
    vim \
    python3 \
    python-is-python3 \
    curl \
    language-pack-en \
    rsync \
    screen \
    sed \
    && update-locale \
    && rm -rf /var/lib/apt/lists/*