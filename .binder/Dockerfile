# -*- mode: dockerfile -*-
# vi: set ft=dockerfile :

# TODO(eric.cousineau): Figure out how to make JupyterLab work with this setup:
# https://github.com/binder-examples/jupyterlab

# TODO(eric.cousineau): See if it's easier to use a conda-based workflow, or a
# simpler Docker base image, to use Eigen headers, rather than doing a custom
# Docker image:
# https://mybinder.readthedocs.io/en/latest/using/config_files.html

FROM quay.io/jupyter/base-notebook
LABEL org.opencontainers.image.source=https://github.com/daveb-dev/project_binder
LABEL org.opencontainers.image.description="GP environment"
LABEL org.opencontainers.image.licenses=MIT
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
     cmake -DPYBIND11_FINDPYTHON=ON -DPYTHON_EXECUTABLE=$(python -c "import sys; print(sys.executable)") -DDOWNLOAD_CATCH=ON -DPYBIND11_TEST=OFF ..  &&\
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
RUN cd myproject/fmca  && \
    git fetch  && \
    cd ..
    
RUN echo "Python directory lib ${PYTHON_LIBRARY_DIR}"
RUN echo "install(TARGETS FMCA" >> myproject/fmca/py/CMakeLists.txt && \
    echo "    COMPONENT python" >> myproject/fmca/py/CMakeLists.txt && \
    echo '    LIBRARY DESTINATION ${PYTHON_LIBRARY_DIR})' >> myproject/fmca/py/CMakeLists.txt && \
    sed -i 's/add_subdirectory(tests)/#add_subdirectory(tests)/g' myproject/fmca/CMakeLists.txt
 
 
     
RUN cat myproject/fmca/py/CMakeLists.txt
RUN mkdir myproject/fmca/build  && cd myproject/fmca/build && \
    cmake -DPYTHON_LIBRARY_DIR=/opt/conda/lib/python3.11/site-packages -DCMAKE_CXX_COMPILER=g++ -DPYBIND11_DIR=/usr/local/share/pybind11/cmake/pybind11  -DPYTHON_EXECUTABLE=$(python -c "import sys; print(sys.executable)") ..  && \
    make && \
    make install

RUN chown -R $NB_UID:$NB_GID \
  "$HOME"
    

RUN chown -R $NB_UID:$NB_GID \
  "myproject"
