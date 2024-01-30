FROM ubuntu:22.04

LABEL maintainer="Erik Osinga <erik.osinga@utoronto.ca>"

USER root

WORKDIR /tmp

# Install packages
RUN apt-get update --yes --quiet --fix-missing \
    && apt-get install --yes --quiet acl \
        bzip2 \
        curl \
        git \
        locales \
        nano \
        rsync \
        openssh-client \
        sudo \
        vim \
        wget \
        zsh \
        tcsh \
    && apt-get clean --yes \
    && apt-get autoremove --purge --quiet --yes \
    && rm -rf /var/lib/apt/lists/* /var/tmp/* 

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
	tqdm
	
# Install other python packages with pip
RUN pip install RM-Tools \
	RMTable \
	PolSpectra \
	gspread \
        PyMultiNest \
	--no-cache-dir
	
# dont get jupyter in the headless version 
# RUN mamba install jupyterlab

# Finally get the POSSUM pipeline into the Docker
## git clone doesnt work but this puts the pipeline in the WORKDIR /tmp
ADD ./POSSUM_Polarimetry_Pipeline ./POSSUM_Polarimetry_Pipeline
