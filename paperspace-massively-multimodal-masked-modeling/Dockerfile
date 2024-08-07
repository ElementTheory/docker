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
    LABEL desc="PaperSpace Gradient base image modified for Apple's 4M: Massively Multimodal Masked Modeling"

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
            python3.9 \
            python3.9-dev \
            python3.9-venv \
            python3-distutils-extra

    # Add symlink so python and python3 commands use same python3.9 executable
    RUN ln -s /usr/bin/python3.9 /usr/local/bin/python3 && \
        ln -s /usr/bin/python3.9 /usr/bin/python

    # Installing pip
    RUN curl -sS https://bootstrap.pypa.io/get-pip.py | python3
    ENV PATH=$PATH:/root/.local/bin    


# ==================================================================
# 4M Dependencies
# ------------------------------------------------------------------
    
    # Based on https://github.com/apple/ml-4m/blob/main/pyproject.toml

    RUN $PIP_INSTALL \
        torch>=2.3.0 \
        torchvision>=0.16.0 \
        albumentations>=1.4.0 \
        boto3>=1.26.16 \
        braceexpand>=0.1.7 \
        diffusers==0.20.0 \
        einops>=0.7.0 \
        ftfy==6.1.0 \
        huggingface_hub>=0.20.0 \
        matplotlib>=3.6.2 \
        networkx==3.2.1 \
        numpy>=1.26.4 \
        opencv_python>=4.9.0.80 \
        opencv_python_headless>=4.6.0.66 \
        pandas>=1.5.2 \    
        Pillow>=9.3.0 \
        PyYAML>=6.0 \
        regex>=2022.10.31 \
        Requests>=2.31.0 \
        scikit_learn>=1.1.3 \
        setuptools>=61.0 \
        tokenizers>=0.15.2 \
        datasets>=0.17 \
        torchmetrics>=1.3.1 \
        tqdm>=4.64.1 \
        xformers>=0.0.24 \
        wandb>=0.13.5 \
        webdataset>=0.2.86

    RUN $PIP_INSTALL 'git+https://github.com/facebookresearch/detectron2.git'

# ==================================================================
# JupyterLab
# ------------------------------------------------------------------

    # Based on https://jupyterlab.readthedocs.io/en/stable/getting_started/installation.html#pip

    RUN  $PIP_INSTALL jupyterlab==4.2.0


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
# Download 4M, and make it available in sys.path
# ------------------------------------------------------------------

    RUN git clone https://github.com/apple/ml-4m

    RUN touch add_4m_to_sys_path.py && \
        echo 'import sys; sys.path.append("/ml-4m")' > add_4m_to_sys_path.py && \
        python add_4m_to_sys_path.py && \ 
        rm add_4m_to_sys_path.py

# ==================================================================
# Startup
# ------------------------------------------------------------------

    EXPOSE 8888 6006

    CMD jupyter lab --allow-root --ip=0.0.0.0 --no-browser --ServerApp.trust_xheaders=True --ServerApp.disable_check_xsrf=False --ServerApp.allow_remote_access=True --ServerApp.allow_origin='*' --ServerApp.allow_credentials=True