FROM nvidia/cuda:11.7.1-cudnn8-devel-ubuntu22.04


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
RUN wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
    && bash Miniconda3-latest-Linux-x86_64.sh -b -p /root/miniconda \
    && rm Miniconda3-latest-Linux-x86_64.sh
# RUN wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-aarch64.sh \
#     && bash Miniconda3-latest-Linux-aarch64.sh -b -p /root/miniconda \
#     && rm Miniconda3-latest-Linux-aarch64.sh

# Set path to conda
ENV PATH=/root/miniconda/bin:$PATH
# Set path to conda environments folder
ENV CONDA_ENVS_PATH=/myhome/.virtualenvs
# Add . /opt/conda/etc/profile.d/conda.sh to ~/.profile
RUN echo "source /root/miniconda/etc/profile.d/conda.sh" >> ~/.profile
# Install Miniconda -- finish here

# Install VS-code -- start here
# Install necessary dependencies
RUN apt-get update && \
    apt-get install -y curl && \
    curl -fsSL https://code-server.dev/install.sh | sh

# Install Python and Jupyter extensions
RUN code-server --install-extension ms-python.python && \
    code-server --install-extension ms-toolsai.jupyter    

# Copy the extensions folder
RUN ln -s /root/.local/share/code-server /root/.vscode-server
# Right now, this solution require to restart the connection to VS-code server to get the plugins working
# Install VS-code -- finish here

# Install nvtop
RUN apt-get update && apt-get install -y \
    nvtop \
    && update-locale \
    && rm -rf /var/lib/apt/lists/*

# To permanently store the WandB key
RUN ln -s /myhome/.netrc /root/.netrc

# Install SSH server -- start here
RUN apt-get update && apt-get install -y \
    openssh-server \
    && update-locale \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir /var/run/sshd
RUN echo 'root:root' | chpasswd
RUN sed -i 's/#*PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
RUN sed -i 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' /etc/pam.d/sshd

ENV NOTVISIBLE="in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
# Install SSH server -- finish here