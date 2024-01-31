FROM ubuntu:22.04

LABEL maintainer="CANFAR Project <support@canfar.net>"

USER root

WORKDIR /tmp

RUN apt-get update --yes --quiet --fix-missing \
    && apt-get install --yes --quiet acl \
        sssd \
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
ARG PYTHON_VERSION

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

RUN mamba update --all --yes \
    && mamba install \
    conda \
    pip \
    conda-libmamba-solver \
    pip-tools \
    && mamba clean --all --quiet --force --yes
