FROM ubuntu:22.04

LABEL maintainer="Erik Osinga <erik.osinga@utoronto.ca>"

USER root

WORKDIR /tmp

# Install packages
RUN apt-get update --yes --quiet --fix-missing \
    && apt-get install --yes --quiet \
        bzip2 \
        curl \
        git \
        locales \
        nano \
        rsync \
        sssd \
        acl \
        openssh-client \
        sudo \
        vim \
        unzip \
        wget \
        zsh \
        tcsh \
    && apt-get clean --yes \
    && apt-get autoremove --purge --quiet --yes \
    && rm -rf /var/lib/apt/lists/* /var/tmp/* 

# Install rclone
RUN sudo -v ; curl https://rclone.org/install.sh | sudo bash

ADD nsswitch.conf /etc/

# Mircomamba Installation
ARG PYTHON_VERSION=3.11

ENV CONDA_DIR=/opt/conda
ENV PATH="${CONDA_DIR}/bin:${PATH}"

COPY condarc "${CONDA_DIR}/.condarc"
RUN set -x \
    && curl -Ls https://micromamba.snakepit.net/api/micromamba/linux-64/latest | tar -xvj bin/micromamba \
    && ./bin/micromamba install \
        --root-prefix="${CONDA_DIR}" \
        --prefix="${CONDA_DIR}" \
        --yes \
        python=${PYTHON_VERSION} mamba

# conda profile scripts by default
RUN ln -s ${CONDA_DIR}/etc/profile.d/conda.sh /etc/profile.d/conda.sh

# install python packages
RUN mamba install numpy \
	astropy \
	matplotlib \
	astropy \
	configparser \
	healpy \
	tqdm \
	progressbar2 
	
# Install other python packages with pip
RUN pip install RM-Tools \
	RMTable \
	PolSpectra \
	gspread \
        psrecord \
        yappi \
        gprof2dot \
	--no-cache-dir
	
# Get PyMultiNest dependencies
RUN apt-get update --yes --quiet --fix-missing \
    && apt-get install --yes --quiet \
        libblas3 \
        libblas-dev \
        liblapack3 \
        liblapack-dev \
        libatlas3-base \
        libatlas-base-dev \
        cmake \
        build-essential \
        gfortran \
    && apt-get clean --yes \
    && apt-get autoremove --purge --quiet --yes \
    && rm -rf /var/lib/apt/lists/* /var/tmp/* 

# Build the libraries for PyMultiNest
RUN git clone https://github.com/JohannesBuchner/MultiNest
# Note cd is only valid for this one command (then back to WORKDIR)
RUN cd MultiNest/build && cmake .. && make
# Add it to the path
ENV LD_LIBRARY_PATH=/tmp/MultiNest/lib:$LD_LIBRARY_PATH

# Install PyMultiNest
RUN pip install pymultinest
	
# Get jupyter in this notebook version 
RUN mamba install jupyterlab

# Finally get the POSSUM pipeline into the Docker
## git clone doesnt work for private repos but this puts the pipeline in the WORKDIR

## TODO: can we git clone the pipeline via github actions?


#ADD ./POSSUM_Polarimetry_Pipeline ./POSSUM_Polarimetry_Pipeline
