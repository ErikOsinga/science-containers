FROM prefecthq/prefect:2.16.3-python3.12-kubernetes

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
        unzip \
        sssd \
        tcsh \
        tk \
        busybox \
        libcfitsio-bin \
        libcfitsio-dev \
        default-jre \
        openmpi-bin \
        libopenmpi-dev \
        screen \
    && apt-get clean --yes \
    && apt-get autoremove --purge --quiet --yes \
    && rm -rf /var/lib/apt/lists/* /var/tmp/* 

# install rclone 
RUN sudo -v ; curl https://rclone.org/install.sh | sudo bash

ADD nsswitch.conf /etc/

# Install build deps for Apptainer
RUN apt-get update -qq \
 && apt-get install -y --no-install-recommends \
       build-essential \
       libseccomp-dev \
       pkg-config \
       squashfs-tools \
       cryptsetup \
       uuid-dev \
       libgpgme-dev \
       libbz2-dev \
       liblz4-dev \
       libarchive-dev \
       golang-go \
 && rm -rf /var/lib/apt/lists/*

# Build & install Apptainer from source (no SUID)
ARG APPTAINER_VER=1.2.3
RUN apt-get update -qq \
 && apt-get install -y --no-install-recommends \
       build-essential \
       libseccomp-dev \
       pkg-config \
       squashfs-tools \
       cryptsetup \
       uuid-dev \
       libgpgme-dev \
       libbz2-dev \
       liblz4-dev \
       libarchive-dev \
       golang-go \
 && mkdir -p /usr/local/src \
 && curl -fsSL "https://github.com/apptainer/apptainer/releases/download/v${APPTAINER_VER}/apptainer-${APPTAINER_VER}.tar.gz" \
       | tar xz -C /usr/local/src \
 && cd /usr/local/src/apptainer-${APPTAINER_VER} \
 && ./mconfig --without-suid \
 && make -C builddir \
 && make -C builddir install \
 && cd / \
 && rm -rf /usr/local/src/apptainer-${APPTAINER_VER} \
 && apt-get purge -y --auto-remove build-essential golang-go \
 && rm -rf /var/lib/apt/lists/*


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
    vos \
    pyvo \ 
    cadctap \
    BeautifulSoup4 \
    mpi4py \
    astroquery \
    cartopy \
    psycopg2-binary \
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

# Install bagpipes
RUN pip install bagpipes

# Install fitsverify, code from https://github.com/opencadc/cfht2caom2/blob/57bc174a70772715b0b609908e8e647553515dcd/Dockerfile#L49C1-L59C60

# Create necessary directories
RUN mkdir -p /usr/local/src /usr/src/app

ARG FITSVERIFY_VERSION=4.20
ARG FITSVERIFY_URL=https://heasarc.gsfc.nasa.gov/docs/software/ftools/fitsverify/fitsverify-${FITSVERIFY_VERSION}.tar.gz
ADD ${FITSVERIFY_URL}  /usr/local/src/
RUN cd /usr/local/src \
  && tar xvf fitsverify-${FITSVERIFY_VERSION}.tar.gz \
  && cd fitsverify-${FITSVERIFY_VERSION} \
  && gcc -o fitsverify ftverify.c fvrf_data.c fvrf_file.c fvrf_head.c fvrf_key.c fvrf_misc.c -DSTANDALONE -I/usr/local/include -L/usr/local/lib -lcfitsio -lm -lnsl \
  && cp ./fitsverify /usr/local/bin/ \
  && ldconfig \
  && cd /usr/src/app \
  && rm -rf /usr/local/src/fitsverify-${FITSVERIFY_VERSION}

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

# Get jupyter in the headless version 
RUN pip install jupyterlab
