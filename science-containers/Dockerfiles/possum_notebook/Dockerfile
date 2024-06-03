FROM prefecthq/prefect:2.16.3-python3.12-kubernetes

LABEL maintainer="Erik Osinga <erik.osinga@utoronto.ca>"

USER root

WORKDIR /tmp

RUN apt update
RUN apt-get install software-properties-common
RUN apt update
RUN add-apt-repository ppa:openjdk-r/ppa
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
        unzip \
        sssd \
        tcsh \
        tk \
        busybox \
        libcfitsio-bin \
        openjdk-11-jdk \
    && apt-get clean --yes \
    && apt-get autoremove --purge --quiet --yes \
    && rm -rf /var/lib/apt/lists/* /var/tmp/* 

# install rclone 
RUN sudo -v ; curl https://rclone.org/install.sh | sudo bash

ADD nsswitch.conf /etc/

# install python packages (used to be mamba install)
RUN pip install numpy \
    astropy \
    matplotlib \
    astropy \
    configparser \
    healpy \
    prefect \
    tqdm
    
# Install other python packages with pip
RUN pip install RM-Tools \
    RMTable \
    PolSpectra \
    gspread \
        psrecord \
        yappi \
        gprof2dot \
        mock \
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

# Install POSSUM2CAOM2
ARG OPENCADC_BRANCH=main
ARG OPENCADC_REPO=opencadc
RUN git clone https://github.com/${OPENCADC_REPO}/caom2tools.git && \
    cd caom2tools && \
    git checkout ${OPENCADC_BRANCH} && \
    pip install ./caom2utils && \
    cd ..

RUN pip install git+https://github.com/${OPENCADC_REPO}/caom2pipe@${OPENCADC_BRANCH}#egg=caom2pipe
RUN pip install git+https://github.com/${OPENCADC_REPO}/possum2caom2@${OPENCADC_BRANCH}#egg=possum2caom2

# Get jupyter in the notebook version 
RUN pip install jupyterlab
