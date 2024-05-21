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
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
    
#    pybind11-dev python3-pybind11 \ 
RUN pip install pybind11 
RUN cd /tmp &&  git clone  https://github.com/pybind/pybind11.git   &&\
 	cd /tmp/pybind11 &&  mkdir build && cd build &&\
     cmake -DPYBIND11_FINDPYTHON=ON -DPYTHON_EXECUTABLE=$(python -c "import sys; print(sys.executable)") ..  &&\
     make  &&\
     make install  &&\
     rm -r /tmp/pybind11 
RUN fix-permissions "/home/${NB_USER}"



# Upgrade pip to use newer indices for castxml.
# WARNING: Never upgrade a distribution `pip` on a host system using sudo!
# We are only doing this for a transient Docker image. For a host system, use a
# virtualenv to upgrade pip.

WORKDIR $HOME/myproject

RUN git clone --recursive https://github.com/daveb-dev/project_binder.git myproject
RUN echo "install(TARGETS FMCA \n\
    COMPONENT python \n\
    LIBRARY DESTINATION \"lib/python\${Python3_VERSION_MAJOR}.\${Python3_VERSION_MINOR}/site-packages\" \n\
    ARCHIVE DESTINATION \"lib\" \n\
    RUNTIME DESTINATION \"bin\")" >> myproject/fmca/py/CMakeLists.txt
RUN mkdir myproject/fmca/build  && cd myproject/fmca/build && \
    cmake ..  && \
    make

RUN chown -R $NB_UID:$NB_GID \
  "$HOME"
    

RUN chown -R $NB_UID:$NB_GID \
  "myproject"
