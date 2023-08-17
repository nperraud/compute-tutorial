FROM ubuntu:latest

# Install dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    cmake \
    git \
    wget \
    unzip \
    htop \
    nvtop \
    vim \
    python3 \
    gcc \
    python3-dev \
    python-is-python3 \
    curl \
    language-pack-en \
    rsync \
    screen \
    sed \
    && update-locale \
    && rm -rf /var/lib/apt/lists/*


# Install Miniconda -- start here
# Get Miniconda
# RUN wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
#     && bash Miniconda3-latest-Linux-x86_64.sh -b -p /root/miniconda \
#     && rm Miniconda3-latest-Linux-x86_64.sh
RUN wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-aarch64.sh \
    && bash Miniconda3-latest-Linux-aarch64.sh -b -p /root/miniconda \
    && rm Miniconda3-latest-Linux-aarch64.sh

# Set path to conda
ENV PATH=/root/miniconda/bin:$PATH
# Set path to conda environments folder
ENV CONDA_ENVS_PATH=/myhome/.virtualenvs
# Add . /opt/conda/etc/profile.d/conda.sh to ~/.profile
RUN echo "source /root/miniconda/etc/profile.d/conda.sh" >> ~/.profile
# Install Miniconda -- finish here