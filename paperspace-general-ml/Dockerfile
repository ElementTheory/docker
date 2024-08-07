#!/bin/bash

# Located in Dockerhub registry: elementtheory/paperspace_gradient_base

# ==================================================================
# Initial setup
# ------------------------------------------------------------------

    # Ubuntu 22.04 as base image
    FROM ubuntu:22.04

    LABEL vendor="Element Theory"
    LABEL author="Andrew Rindfleisch, Element Theory"
    LABEL version="1.0"
    LABEL desc="Modified version of PaperSpace Gradient base image"

    RUN yes | unminimize

    # Set ENV variables
    ENV LANG C.UTF-8
    ENV SHELL=/bin/bash
    ENV DEBIAN_FRONTEND=noninteractive

    ENV APT_INSTALL="apt-get install -y --no-install-recommends"
    ENV PIP_INSTALL="python3 -m pip --no-cache-dir install --upgrade"
    ENV GIT_CLONE="git clone --depth 10"


# ==================================================================
# Tools
# ------------------------------------------------------------------

    RUN apt-get update && \
        $APT_INSTALL \
            apt-transport-https \
            apt-utils \
            awscli \
            build-essential \
            ca-certificates \
            cifs-utils \
            csvkit \
            curl \
            dialog \
            emacs \
            ffmpeg \
            gcc \
            git \
            gnupg \
            iputils-ping \
            joe \
            jq \
            libboost-all-dev \
            libsm6 \
            libssl-dev \
            libxext6 \
            make \
            man-db \
            manpages \
            manpages-dev \
            manpages-posix \
            manpages-posix-dev \
            mlocate \
            nano \
            openssh-client \
            pkg-config \
            rsync \
            software-properties-common \
            sudo \
            unrar \
            unzip \
            vim \
            wget \
            zip \
            zlib1g

# ==================================================================
# Git-lfs
# ------------------------------------------------------------------
    
    RUN curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | sudo bash && \
        $APT_INSTALL git-lfs


# ==================================================================
# Python
# ------------------------------------------------------------------

    #Based on https://launchpad.net/~deadsnakes/+archive/ubuntu/ppa

    # Adding repository for python3.12
    RUN add-apt-repository ppa:deadsnakes/ppa -y && \
        $APT_INSTALL \
            python3.11 \
            python3.11-dev \
            python3.11-venv \
            python3-distutils-extra

    # Add symlink so python and python3 commands use same python3.9 executable
    RUN ln -s /usr/bin/python3.11 /usr/local/bin/python3 && \
        ln -s /usr/bin/python3.11 /usr/bin/python

    # Installing pip
    RUN curl -sS https://bootstrap.pypa.io/get-pip.py | python3
    ENV PATH=$PATH:/root/.local/bin    

# ==================================================================
# Installing CUDA packages (CUDA Toolkit 12.4 and CUDNN 9.1.1)
# ------------------------------------------------------------------

    RUN wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-ubuntu2204.pin && \
        mv cuda-ubuntu2204.pin /etc/apt/preferences.d/cuda-repository-pin-600  && \
        wget https://developer.download.nvidia.com/compute/cuda/12.4.1/local_installers/cuda-repo-ubuntu2204-12-4-local_12.4.1-550.54.15-1_amd64.deb  && \
        dpkg -i cuda-repo-ubuntu2204-12-4-local_12.4.1-550.54.15-1_amd64.deb  && \
        cp /var/cuda-repo-ubuntu2204-12-4-local/cuda-*-keyring.gpg /usr/share/keyrings/  && \
        apt-get update  && \
        $APT_INSTALL cuda-toolkit-12-4 && \
        rm cuda-repo-ubuntu2204-12-4-local_12.4.1-550.54.15-1_amd64.deb
        

    # Installing CUDNN
    RUN wget https://developer.download.nvidia.com/compute/cudnn/9.1.1/local_installers/cudnn-local-repo-ubuntu2204-9.1.1_1.0-1_amd64.deb && \
        dpkg -i cudnn-local-repo-ubuntu2204-9.1.1_1.0-1_amd64.deb && \
        cp /var/cudnn-local-repo-ubuntu2204-9.1.1/cudnn-*-keyring.gpg /usr/share/keyrings/ && \
        apt-get update && \
        $APT_INSTALL cudnn-cuda-12 && \
        rm cudnn-local-repo-ubuntu2204-9.1.1_1.0-1_amd64.deb

    ENV PATH=$PATH:/usr/local/cuda/bin
    ENV LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH


# ==================================================================
# PyTorch
# ------------------------------------------------------------------

    # Based on https://pytorch.org/get-started/locally/

    RUN $PIP_INSTALL \
        torch==2.3.0 \
        torchvision==0.18.0 \
        torchaudio==2.3.0 \
        --extra-index-url https://download.pytorch.org/whl/cu121

# ==================================================================
# TensorFlow
# ------------------------------------------------------------------

    # Based on https://www.tensorflow.org/install/pip

    RUN $PIP_INSTALL tensorflow[and-cuda]==2.16.1

# ==================================================================
# Hugging Face
# ------------------------------------------------------------------
    
    # Based on https://huggingface.co/docs/transformers/installation
    # Based on https://huggingface.co/docs/datasets/installation

    RUN $PIP_INSTALL \
        accelerate==0.30.1 \
        datasets==2.19.1 \
        diffusers==0.27.2 \
        peft==0.10.0 \
        safetensors==0.4.3 \
        tokenizers \
        transformers==4.40.2


# ==================================================================
# JupyterLab
# ------------------------------------------------------------------

    # Based on https://jupyterlab.readthedocs.io/en/stable/getting_started/installation.html#pip

    RUN  $PIP_INSTALL jupyterlab==4.2.0


# ==================================================================
# Additional Python Packages
# ------------------------------------------------------------------

    RUN $PIP_INSTALL \ 
        bitsandbytes==0.41.2 \
        boto3==1.28.51 \
        cloudpickle==2.2.1 \
        cupy-cuda12x==12.2.0 \
        cython==3.0.2 \
        deepspeed==0.10.3 \
        future==0.18.3 \
        gdown==4.7.1 \
        gradient==2.0.6 \
        ipywidgets==8.1.1 \
        jsonify==0.5 \
        matplotlib==3.8.4 \
        nltk==3.8.1 \
        omegaconf==2.3.0 \
        opencv-python==4.8.0.76 \
        openpyxl==3.1.2 \
        pillow==9.5.0 \
        pyyaml==5.4.1 \
        scikit-image==0.23.2 \
        scikit-learn==1.4.2 \
        scipy==1.13.0 \
        seaborn==0.12.2 \
        sentence-transformers==2.2.2 \
        spacy==3.6.1 \
        sqlalchemy==2.0.21 \
        tabulate==0.9.0 \
        timm==0.9.7 \
        tqdm==4.66.1 \
        wandb==0.15.10 \
        xgboost==1.7.6 

    RUN $PIP_INSTALL attrs==23.1.0


# ==================================================================
# CMake
# ------------------------------------------------------------------

    RUN $GIT_CLONE https://github.com/Kitware/CMake ~/cmake && \
        cd ~/cmake && \
        ./bootstrap && \
        make -j"$(nproc)" install


# ==================================================================
# Node.js and Jupyter Notebook Extensions
# ------------------------------------------------------------------

    RUN curl -fsSL https://deb.nodesource.com/setup_20.x | sudo bash && \
        $APT_INSTALL nodejs

    RUN $PIP_INSTALL jupyterlab-git==0.50.0
     

# ==================================================================
# Startup
# ------------------------------------------------------------------

    EXPOSE 8888 6006

    CMD jupyter lab --allow-root --ip=0.0.0.0 --no-browser --ServerApp.trust_xheaders=True --ServerApp.disable_check_xsrf=False --ServerApp.allow_remote_access=True --ServerApp.allow_origin='*' --ServerApp.allow_credentials=True