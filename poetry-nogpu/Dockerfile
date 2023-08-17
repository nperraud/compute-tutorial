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


# Install poetry -- start here
RUN curl -sSL https://install.python-poetry.org | python3 -
# add the path to poetry to the PATH variable $HOME/.local/bin in the bashrc file
# RUN echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
ENV PATH="/root/.local/bin:${PATH}"

# Set up the folder where the poetry virtual environment will be stored 
# RUN echo 'export POETRY_VIRTUALENVS_PATH="/myhome/.virtualenvs"' >> ~/.bashrc
ENV POETRY_VIRTUALENVS_PATH="/myhome/.virtualenvs"
# Install poetry -- finish here