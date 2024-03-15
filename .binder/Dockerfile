# -*- mode: dockerfile -*-
# vi: set ft=dockerfile :

# TODO(eric.cousineau): Figure out how to make JupyterLab work with this setup:
# https://github.com/binder-examples/jupyterlab

# TODO(eric.cousineau): See if it's easier to use a conda-based workflow, or a
# simpler Docker base image, to use Eigen headers, rather than doing a custom
# Docker image:
# https://mybinder.readthedocs.io/en/latest/using/config_files.html

FROM quay.io/jupyter/base-notebook

USER root
# Avoid prompts from apt
ENV DEBIAN_FRONTEND=noninteractive

# Update and install basic required packages
RUN apt-get update && apt-get install -y \
    build-essential \
    cmake \
    g++ \
    make \
    python3 \
    python3-pip \
    python3-venv \
    libxml2 \
    libxml2-dev \
    libeigen3-dev \
    libstdc++-11-dev  \
    git cmake \
    pybind11-dev python3-pybind11 \ 
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*


RUN fix-permissions "/home/${NB_USER}"



# Upgrade pip to use newer indices for castxml.
# WARNING: Never upgrade a distribution `pip` on a host system using sudo!
# We are only doing this for a transient Docker image. For a host system, use a
# virtualenv to upgrade pip.

WORKDIR $HOME/myproject

RUN git clone --recursive https://github.com/daveb-dev/project_binder.git myproject

RUN mkdir myproject/fmca/build  && cd myproject/fmca/build && \
    cmake ..  && \
    make

RUN chown -R $NB_UID:$NB_GID \
  "$HOME"
    

RUN chown -R $NB_UID:$NB_GID \
  "myproject"
