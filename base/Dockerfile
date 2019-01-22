# for a docker image with:
# CUDA 9.0 + cuDNN 7.x
# NCCL 2.x
# Anaconda 5.2 (python 3.6.5)
# cuda_cudnn_ana_base:9.0-7.2-5.3
# cuda_cudnn_ana_base:latest

# tensorflow dockerfile
# line 1 - 51
FROM nvidia/cuda:9.0-base-ubuntu16.04

# the maintainer information
LABEL maintainer "Teng Fu <teng.fu@teleware.com>"

# cuDNN installation
# NCCL installation
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        cuda-command-line-tools-9-0 \
        cuda-cublas-dev-9-0 \
        cuda-cudart-dev-9-0 \
        cuda-cufft-dev-9-0 \
        cuda-curand-dev-9-0 \
        cuda-cusolver-dev-9-0 \
        cuda-cusparse-dev-9-0 \
        libcudnn7=7.2.1.38-1+cuda9.0 \
        libcudnn7-dev=7.2.1.38-1+cuda9.0 \
        libnccl2=2.2.13-1+cuda9.0 \
        libnccl-dev=2.2.13-1+cuda9.0 \
        libcurl3-dev \
        libfreetype6-dev \
        libhdf5-serial-dev \
        libpng12-dev \
        libzmq3-dev \
        pkg-config \
        python-dev \
        rsync \
        software-properties-common \
        unzip \
        zip \
        zlib1g-dev \
        wget \
        curl \
        git \
        && \
    rm -rf /var/lib/apt/lists/* && \
    find /usr/local/cuda-9.0/lib64/ -type f -name 'lib*_static.a' -not -name 'libcudart_static.a' -delete && \
    rm /usr/lib/x86_64-linux-gnu/libcudnn_static_v7.a

RUN apt-get update && \
        apt-get install nvinfer-runtime-trt-repo-ubuntu1604-4.0.1-ga-cuda9.0 && \
        apt-get update && \
        apt-get install libnvinfer4=4.1.2-1+cuda9.0 && \
        apt-get install libnvinfer-dev=4.1.2-1+cuda9.0

# Link NCCL libray and header where the build script expects them.
RUN mkdir /usr/local/cuda-9.0/lib &&  \
    ln -s /usr/lib/x86_64-linux-gnu/libnccl.so.2 /usr/local/cuda/lib/libnccl.so.2 && \
    ln -s /usr/include/nccl.h /usr/local/cuda/include/nccl.h

# pip installation
RUN curl -fSsL -O https://bootstrap.pypa.io/get-pip.py && \
    python get-pip.py && \
    rm get-pip.py

# -------------------------------------------
# Anaconda installation
# https://github.com/ContinuumIO/docker-images/blob/master/anaconda/Dockerfile
ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV PATH /opt/conda/bin:$PATH

RUN apt-get update --fix-missing && apt-get install -y wget bzip2 ca-certificates \
    libglib2.0-0 libxext6 libsm6 libxrender1 \
    git mercurial subversion

# download and install the anaconda
# anaconda 2 is python 2.7
# anaconda 3 is python 3.6 (after 4.2.0 is python 3.6, below is python3.5)
RUN wget --quiet https://repo.anaconda.com/archive/Anaconda3-5.2.0-Linux-x86_64.sh -O ~/anaconda.sh && \
    /bin/bash ~/anaconda.sh -b -p /opt/conda && \
    rm ~/anaconda.sh && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc

# get tini and installed
# tini is PID management software to avoid any abuse of PID in OS level
RUN apt-get install -y curl grep sed dpkg && \
    TINI_VERSION=`curl https://github.com/krallin/tini/releases/latest | grep -o "/v.*\"" | sed 's:^..\(.*\).$:\1:'` && \
    curl -L "https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini_${TINI_VERSION}.deb" > tini.deb && \
    dpkg -i tini.deb && \
    rm tini.deb && \
    apt-get clean

# part of the tensorflow dockder file for tensorflow build
# line 53-67
RUN pip --no-cache-dir install \
        keras_applications \
        keras_preprocessing \
        mock \
        && \
    python -m ipykernel.kernelspec

# Set up our notebook config.
COPY jupyter_notebook_config.py /root/.jupyter/

# Jupyter has issues with being run directly:
#   https://github.com/ipython/ipython/issues/7062
# We just add a little wrapper script.
COPY run_jupyter.sh /

# the default volume shared with host machine directory
# in the docker run 
# use docker run -v <absolute path of local folder>:/app
RUN mkdir /app

WORKDIR /app

# TensorBoard
EXPOSE 6006
# IPython
EXPOSE 8888

# automatic run this bash file 
# because it is currently installed with anaconda, so must specified the bash's path
CMD [ "/bin/bash", "/run_jupyter.sh", "--allow-root"]