# GROMACS 2020
# 
# Contents:
#   Ubuntu 16.04
#   CUDA version 10.1
#   GNU compilers (upstream)
#   OFED (upstream)
#   OpenMPI version 3.1.4

FROM nvcr.io/nvidia/cuda:11.7.0-devel-ubuntu22.04 AS build

# GNU compiler
RUN apt-get update -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        g++ \
        gcc && \
    rm -rf /var/lib/apt/lists/*

# Python
RUN apt-get update -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        python3 && \
    rm -rf /var/lib/apt/lists/*

# pip
RUN apt-get update -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        python3-pip \
        python3-setuptools \
        python3-wheel && \
    rm -rf /var/lib/apt/lists/*
RUN pip --no-cache-dir install numpy

# CMake version 3.25.1
RUN apt-get update -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        make \
        wget && \
    rm -rf /var/lib/apt/lists/*
RUN mkdir -p /var/tmp && wget -q -nc --no-check-certificate -P /var/tmp https://github.com/Kitware/CMake/releases/download/v3.25.1/cmake-3.25.1-linux-x86_64.sh && \
    mkdir -p /usr/local && \
    /bin/sh /var/tmp/cmake-3.25.1-linux-x86_64.sh --prefix=/usr/local --skip-license && \
    rm -rf /var/tmp/cmake-3.25.1-linux-x86_64.sh
ENV PATH=/usr/local/bin:$PATH

# OFED
RUN apt-get update -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends -t jammy \
        dapl2-utils \
        ibutils \
        ibverbs-providers \
        ibverbs-utils \
        infiniband-diags \
        libdapl-dev \
        libdapl2 \
        libibmad-dev \
        libibmad5 \
        libibverbs-dev \
        libibverbs1 \
        librdmacm-dev \
        librdmacm1 \
        rdmacm-utils && \
    rm -rf /var/lib/apt/lists/*

# OpenMPI version 4.1.4
RUN apt-get update -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        bzip2 \
        file \
        hwloc \
        libnuma-dev \
        make \
        openssh-client \
        perl \
        tar \
        wget && \
    rm -rf /var/lib/apt/lists/*
RUN mkdir -p /var/tmp && wget -q -nc --no-check-certificate -P /var/tmp https://www.open-mpi.org/software/ompi/v4.1/downloads/openmpi-4.1.4.tar.bz2 && \
    mkdir -p /var/tmp && tar -x -f /var/tmp/openmpi-4.1.4.tar.bz2 -C /var/tmp -j && \
    cd /var/tmp/openmpi-4.1.4 &&   ./configure --prefix=/usr/local/openmpi --disable-getpwuid --enable-orterun-prefix-by-default --with-cuda --with-verbs && \
    make -j$(nproc) && \
    make -j$(nproc) install && \
    rm -rf /var/tmp/openmpi-4.1.4 /var/tmp/openmpi-4.1.4.tar.bz2
ENV LD_LIBRARY_PATH=/usr/local/openmpi/lib:$LD_LIBRARY_PATH \
    PATH=/usr/local/openmpi/bin:$PATH

# http://ftp.gromacs.org/pub/gromacs/gromacs-2023.2.tar.gz
RUN mkdir -p /var/tmp && wget -q -nc --no-check-certificate -P /var/tmp http://ftp.gromacs.org/pub/gromacs/gromacs-2023.2.tar.gz && \
    mkdir -p /var/tmp && tar -x -f /var/tmp/gromacs-2023.2.tar.gz -C /var/tmp -z && \
    mkdir -p /var/tmp/gromacs-2023.2/build && cd /var/tmp/gromacs-2023.2/build && cmake -DCMAKE_INSTALL_PREFIX=/usr/local/gromacs/2023.2 -D CMAKE_BUILD_TYPE=Release -D CUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda -D GMX_BUILD_OWN_FFTW=ON -D GMX_GPU=CUDA -D GMX_MPI=ON -D REGRESSIONTEST_DOWNLOAD=ON -D GMX_OPENMP=ON -D GMX_PREFER_STATIC_LIBS=ON -D MPIEXEC_PREFLAGS=--allow-run-as-root /var/tmp/gromacs-2023.2 && \
    cmake --build /var/tmp/gromacs-2023.2/build --target all -- -j$(nproc) && \
    cmake --build /var/tmp/gromacs-2023.2/build --target install -- -j$(nproc) && \
    rm -rf /var/tmp/gromacs-2023.2 /var/tmp/gromacs-2023.2.tar.gz

ENV PATH=$PATH:/usr/local/gromacs/2023.2/bin

LABEL gromacs.version=2023.2

# http://ftp.gromacs.org/pub/gromacs/gromacs-2020.7.tar.gz
RUN mkdir -p /var/tmp && wget -q -nc --no-check-certificate -P /var/tmp http://ftp.gromacs.org/pub/gromacs/gromacs-2020.7.tar.gz && \
    mkdir -p /var/tmp && tar -x -f /var/tmp/gromacs-2020.7.tar.gz -C /var/tmp -z && \
    mkdir -p /var/tmp/gromacs-2020.7/build && cd /var/tmp/gromacs-2020.7/build && cmake -DCMAKE_INSTALL_PREFIX=/usr/local/gromacs/2020.7 -D CMAKE_BUILD_TYPE=Release -D CUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda -D GMX_BUILD_OWN_FFTW=ON -D GMX_GPU=ON -D REGRESSIONTEST_DOWNLOAD=ON /var/tmp/gromacs-2020.7 && \
    cmake --build /var/tmp/gromacs-2020.7/build --target all -- -j$(nproc) && \
    cmake --build /var/tmp/gromacs-2020.7/build --target install -- -j$(nproc) && \
    rm -rf /var/tmp/gromacs-2020.7 /var/tmp/gromacs-2020.7.tar.gz

ENV PATH=$PATH:/usr/local/gromacs/2020.7/bin

LABEL gromacs.version=2020.7

# http://ftp.gromacs.org/pub/gromacs/gromacs-2021.6.tar.gz
RUN mkdir -p /var/tmp && wget -q -nc --no-check-certificate -P /var/tmp http://ftp.gromacs.org/pub/gromacs/gromacs-2021.6.tar.gz && \
    mkdir -p /var/tmp && tar -x -f /var/tmp/gromacs-2021.6.tar.gz -C /var/tmp -z && \
    mkdir -p /var/tmp/gromacs-2021.6/build && cd /var/tmp/gromacs-2021.6/build && cmake -DCMAKE_INSTALL_PREFIX=/usr/local/gromacs/2021.6 -D CMAKE_BUILD_TYPE=Release -D CUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda -D GMX_BUILD_OWN_FFTW=ON -D GMX_GPU=CUDA -D REGRESSIONTEST_DOWNLOAD=ON /var/tmp/gromacs-2021.6 && \
    cmake --build /var/tmp/gromacs-2021.6/build --target all -- -j$(nproc) && \
    cmake --build /var/tmp/gromacs-2021.6/build --target install -- -j$(nproc) && \
    rm -rf /var/tmp/gromacs-2021.6 /var/tmp/gromacs-2021.6.tar.gz

ENV PATH=$PATH:/usr/local/gromacs/2021.6/bin

LABEL gromacs.version=2021.6

FROM nvcr.io/nvidia/cuda:11.7.0-devel-ubuntu22.04

# GNU compiler runtime
RUN apt-get update -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        libgomp1 && \
    rm -rf /var/lib/apt/lists/*

# Python
RUN apt-get update -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        python3 && \
    rm -rf /var/lib/apt/lists/*

# OFED
RUN apt-get update -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends -t jammy \
        dapl2-utils \
        ibutils \
        ibverbs-providers \
        ibverbs-utils \
        infiniband-diags \
        libdapl-dev \
        libdapl2 \
        libibmad-dev \
        libibmad5 \
        libibverbs-dev \
        libibverbs1 \
        librdmacm-dev \
        librdmacm1 \
        rdmacm-utils && \
    rm -rf /var/lib/apt/lists/*

# OpenMPI
RUN apt-get update -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        hwloc \
        openssh-client && \
    rm -rf /var/lib/apt/lists/*
COPY --from=build /usr/local/openmpi /usr/local/openmpi
ENV LD_LIBRARY_PATH=/usr/local/openmpi/lib:$LD_LIBRARY_PATH \
    PATH=/usr/local/openmpi/bin:$PATH

# http://ftp.gromacs.org/pub/gromacs/gromacs-2023.2.tar.gz
COPY --from=build /usr/local/gromacs/2023.2 /usr/local/gromacs/2023.2

# http://ftp.gromacs.org/pub/gromacs/gromacs-2020.7.tar.gz
COPY --from=build /usr/local/gromacs/2020.7 /usr/local/gromacs/2020.7

# http://ftp.gromacs.org/pub/gromacs/gromacs-2021.6.tar.gz
COPY --from=build /usr/local/gromacs/2021.6 /usr/local/gromacs/2021.6

ENV PATH=$PATH:/usr/local/gromacs/2023.2/bin

LABEL gromacs.version=2023.2

ENV PATH=$PATH:/usr/local/gromacs/2020.7/bin

LABEL gromacs.version=2020.7

ENV PATH=$PATH:/usr/local/gromacs/2021.6/bin

LABEL gromacs.version=2021.6
