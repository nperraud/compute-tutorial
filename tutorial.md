# Practial session 19 october 2023

## Requirements

For this tutorial, you will need to:
1. BYOC: Bring your own code (something portable that does not depend on a big amount of data or weird dependencies)
    - Your python environement should be defined using one of the following:
        * Conda: environment.yaml
        * Poetry: pyproject.toml
        * Pip: requirements.txt
    - Extra software that can be installed in ubuntu via `apt-get`
2. Having docker installed on your maching
3. Having a dockerhub account
4. Having a runai account
5. Having a cscs account
6. Having at least 20Gb free on your machine 


## Running simple python workloads at CSCS

### Introduction and connection to CSCS
* What is a good ssh configuration for CSCS
* What is the best way to deal with the 2FA: https://github.com/eth-cscs/sshservice-cli
* How to transfer files to CSCS: rsync + where should they be stored

### Miniconda installation

Install miniconda in your user folder using:
```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
    && bash Miniconda3-latest-Linux-x86_64.sh -b -p $HOME/miniconda \
    && rm Miniconda3-latest-Linux-x86_64.sh
```

Add the following lines to your `.bashrc` file:
```bash
# Set path to conda
export PATH=$HOME/miniconda/bin:$PATH
# Set path to conda environments folder
export CONDA_ENVS_PATH=$HOME/.virtualenvs
# Add . /opt/conda/etc/profile.d/conda.sh to ~/.profile
echo "source $HOME/miniconda/etc/profile.d/conda.sh" >> ~/.profile
```

Create a new conda environment:
```bash
conda create -n myenv python=3.10
```

### Installing dependencies
Which module should be loaded? I guess mostly `daint-gpu`:
```bash
module load daint-gpu
```

### Getting a compute node


### Submitting a job

### Batch file


## Docker

Dockerfile starts from a base image. Here are 3 images that will cover most of your needs:
* Ubuntu for general purpose: `ubuntu:latest`
* Ubuntu with cuda when using a GPU: `nvidia/cuda:11.7.1-cudnn8-devel-ubuntu22.04`
* One with renku?

TODO: short note about tags

### Dockerfile

Let us create a dockerfile that will be used to build our image. We will use the `ubuntu:latest` image as a base. We will install some dependencies and miniconda.

```dockerfile
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
```

Make a folder `ubuntu-nogpu` and put this file in it. Then build the image using the following command:
```bash
mkdir ubuntu-nogpu
cd ubuntu-nogpu
# copy the dockerfile in this folder and name it Dockerfile
docker build -t ubuntu-nogpu .
```

Now run the image:
```bash
docker run -it --rm ubuntu-nogpu bash
```
Play bit with this image. 
* Note that the software we have installed is availlable. Run `htop` for example.
* Install some new software 
```bash
apt-get update
apt-get install python3-dev
```
* Create a new file with `vim` and save it.

Now exit the container using `ctrl+D` or `exit`. 
Restart the container and observe that the file is not there anymore. This is because is destroyed when you exit it. 
To solve this issue, we need to mount a folder from the host to the container. This will allow us to keep the files between sessions.
Similarly, the software you installed is also gone. Software should be installed in the dockerfile to be permanent.
We will explore how to do this later in this tutorial.

Note that containers do not have to be destroid when you exit them. Simply stoping them allow persistence like a machine that you turn off and on again. Try it... Remove the `--rm` argument in the docker run command.  However, for this tutorial, we will assume that the containers will in general be destroyed when we exit them.

#### Using conda

If you want to work with *conda*, you can install miniconda using by adding the following lines to your dockerfile:
```dockerfile
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
```

**Note here that we are setting the `CONDA_ENVS_PATH` to `/myhome/.virtualenvs`. This is because we will mount the `/myhome` folder to a local folder on our machine/runai/cscs. This will allow us to keep our virtual environments between sessions.**

Put this file in a folder and build the image
```bash
    mkdir conda-nogpu
    cd conda-nogpu
    # copy the dockerfile in this folder and name it Dockerfile
    docker build -t conda-nogpu .
```

#### Using poetry

If you want to work with *poetry*, you can install it using by adding the following lines to your dockerfile:
```dockerfile
# Install poetry -- start here
RUN curl -sSL https://install.python-poetry.org | python3 -
# add the path to poetry to the PATH variable $HOME/.local/bin in the bashrc file
# RUN echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
ENV PATH="/root/.local/bin:${PATH}"
# Set up the folder where the poetry virtual environment will be stored 
# RUN echo 'export POETRY_VIRTUALENVS_PATH="/myhome/.virtualenvs"' >> ~/.bashrc
ENV POETRY_VIRTUALENVS_PATH="/myhome/.virtualenvs"
# Install poetry -- finish here
```

**Note here that we are setting the `POETRY_VIRTUALENVS_PATH` to `/myhome/.virtualenvs`. This is because we will mount the `/myhome` folder to a local folder on our machine/runai/cscs. This will allow us to keep our virtual environments between sessions.**

Put this file in a folder and build the image
```bash
    mkdir poetry-nogpu
    cd poetry-nogpu
    # copy the dockerfile in this folder and name it Dockerfile
    docker build --platform linux/amd64 -t poetry-nogpu .
```


## Local exectution
Let us create a directory where we will work

```bash
mkdir ~/compute-tutorial
cd ~/compute-tutorial
```

Make a folder to save your python virtual environment
```bash
mkdir .virtualenv
```

Get the desired code
```bash
git clone https://github.com/nperraud/demopkg.git
```

Start a container with the code mounted. For conda:
```bash
docker run -it --rm -v ~/compute-tutorial:/myhome conda-nogpu bash
```
For poetry:
```bash
docker run -it --rm -v ~/compute-tutorial:/myhome poetry-nogpu bash
```

Here we are using the `conda-nogpu` image we just built. We are mounting the `~/compute-tutorial` folder to `/myhome` in the container. We are also starting a bash shell in the container. The `--rm` flag will remove the container when we exit. The `-it` flags are needed to start an interactive session.

Note also that `bash` is the default command of the container. Later on, we can change it to shell scripts that launch our python code.

Once you are in the container, we need to create a virtual environment and install the code dependencies... This will have to be done only once as we use the folder `/myhome/.virtualenvs` to store permanently the virtual environments.

For a requirements.txt, you will get something like:
```bash
cd /myhome/demopkg
conda create -n demopkg python=3.10
conda activate demopkg
pip install -r requirements.txt
```

For conda, you will get something like:
```bash
cd /myhome/demopkg
conda env create -f environment.yml
conda activate demopkg
```
Note that environment.yml files could depend on the platform (x86 or arm). So you might need to create one for each platform you want to use.

And for poetry, you will get something like:
```bash
cd /myhome/demopkg
poetry shell
poetry install
```

Now you can run your code and check that it works:
```bash
python experiments/train_mlp.py
```

Stop the training using `ctrl+C` and exit the container
```bash
exit
```

You can now make a script that will launch your code. For example, create a file `run.sh` with the following content:
```bash
#!/bin/bash
cd /myhome/demopkg
conda activate demopkg
python experiments/train_mlp.py 
```
Then you would need to add this script to the dockerfile and make it the default command of the container. For example, add the following lines to the dockerfile:
```dockerfile
# Add the run.sh script to the container
COPY run.sh /myhome/run.sh
# Make the script executable
RUN chmod +x /myhome/run.sh
# Set the default command of the container to run.sh
CMD ["/myhome/run.sh"]
```
Then recompile the image and run it again:
```bash
docker build -t conda-nogpu .
docker run -it --rm -v ~/compute-tutorial:/myhome conda-nogpu
``` 
However, this is not convenient. Instead, we can run
```bash
docker run -it --rm -v ~/compute-tutorial:/myhome conda-nogpu bash -cl "cd /myhome/demopkg && conda activate demopkg && python experiments/train_mlp.py" 
```
Here we give the command to run as an argument to the `bash` command. The `-c` flag tells bash to run the command. The `-l` flag tells bash to run the command as if it was a login shell. This is needed to activate the conda environment. The `&&` are used to chain the commands. The `cd /myhome/demopkg` is needed to change the working directory to the folder where the code is. The `conda activate demopkg` is needed to activate the conda environment. The `python experiments/train_mlp.py` is the command to run.


Similarly for poetry, you can run
```bash
docker run -it --rm -v ~/compute-tutorial:/myhome poetry-nogpu bash -cl "cd /myhome/demopkg && poetry run python experiments/train_mlp.py"
```




### Adding support for remote ssh
Remote ssh into a container can be very useful for monitoring and runing a VS-code kernel.

To add an ssh server to your container, you can simply add to your dockerfile:

```dockerfile
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
```
Here we are setting the root password to `root`. This is not secure but it is convenient for a tutorial. You can change it to something more secure. We are also exposing the port 22 of the container to the host. This will allow us to ssh into the container. Finally, `CMD ["/usr/sbin/sshd", "-D"]` is the command that will be run when the container starts. It will start the ssh server.

We recommend you to create a new image for this. Create a new folder with a new dockerfile and add the ssh server to it. For example, create a folder `conda-nogpu-ssh` with a dockerfile containing:

```dockerfile
FROM conda-nogpu
# Install SSH server -- start here
....
# Install SSH server -- finish here
```

Then build the image:
```bash
cd conda-nogpu-ssh
docker build -t conda-nogpu-ssh .
```

Now you can run the container with ssh support:
```bash
docker run -it --rm -v ~/compute-tutorial:/myhome -p 2222:22 conda-nogpu-ssh
```
Here we are exposing the port 22 of the container to the port 2222 of the host. This is needed because the port 22 is already used by the host. You can now ssh into the container using:
```bash
ssh root@localhost -p 2222
```
The password is `root`.

**TODO: Is there a way to ssh without a password using a key? The only way I found is to add the public key to the container. However, it would be better to give access to the container to the authorized keys of the host.**

Now you can connect with VS-code to the container. In VS-code install the `python` and the `jupyter` extension on the remote server. Then navigate to the folder where your code is `/myhome/demopkg` and start a jupyter notebook `notebooks/start.ipynb`. 

Note that the extensions will be installed in the container. So they will not be available when you restart the container. You can add them to the dockerfile to make them permanent.

Now you probably need to stop the container using `docker stop <container_id>`. You can find the container id using `docker ps`. You can also use `docker ps -a` to see all the containers. 


### Adding support for VS-code

To add support for VS-code, we need to install the python extension in the container. We can do this by adding the following lines to the dockerfile:
```dockerfile

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
```

** TODO: this is not working. **

## Adding GPU support, WandB, and architecture support

**GPU:** The dockerfile for GPU is very similar to the one without GPU. The only difference is the base image. For example, for conda, you can use the following:
```dockerfile
FROM nvidia/cuda:11.7.1-cudnn8-devel-ubuntu22.04
...
```
You might also be interested to add nvtop to monitor the GPU usage. You can do this by adding the following lines to the dockerfile:
```dockerfile
# Install nvtop
RUN apt-get update && apt-get install -y \
    nvtop \
    && update-locale \
    && rm -rf /var/lib/apt/lists/*
```

**WandB:** WandB requires to add your key to the container to authenticate. Since it is private, you do not want to add it to the dockerfile. Instead, you can add it to a file that stays in your permanent storage `/myhome`. A symbolic link is sufficient. Simply add these line to your dockerfile:
```dockerfile
# Make a link for wandb
RUN ln -s /myhome/.netrc /root/.netrc
```

**Important - Architecture:** When building an image for Runai or CSCS, you need to ensure that the platform is linux/amd64.
If you are on a mac with an arm processor, you need to add the argument `--platform linux/amd64` to the `docker build` command. Furthermore, be sure that you are installing the right conda version!

You can run `linux/amd64` on an arm mac. It might be a bit slower and you will get the warning: `WARNING: The requested image's platform (linux/amd64) does not match the detected host platform (linux/arm64/v8) and no specific platform was requested`. 

Build a new dockerfile with GPU support in a new folder `conda-gpu-ssh` and build it:
```bash
cd conda-gpu-ssh
docker build -t conda-gpu-ssh --platform linux/amd64 .
```
It will take some time...

We are all set to run our load at CSCS and Runai.


## Renku execution?
@Tasko, can we run these containers on renku? Let us discuss...

## Runai execution

### Intro and connection to Runai

### File transer

### Runing a docker container


## CSCS execution

### Introduction and connection to CSCS
Give all the tips and configuration for the easiest connection to CSCS.

### File transer

### Runing using a job
Run a load without using a docker container... Simple use of conda...

### Runing a container
Run the previouly created container on CSCS.

