# for a docker image with:
# CUDA 10.0.130 
# cuDNN 7.4.1.5
# NCCL 2.4.2
# Anaconda naconda3-2019.07-Linux-x86_64.sh (python 3.7.5)

# base
FROM nvidia/cuda:10.0-devel-ubuntu16.04

# devel-- cudnn
LABEL maintainer "Teng Fu <teng.fu@teleware.com>"

# ENV CUDNN_VERSION 7.6.0.64
# latest tf 1.15 and tf 2.0 rc supports 7.4
ENV CUDNN_VERSION 7.4.1.5

LABEL com.nvidia.cudnn.version="${CUDNN_VERSION}"

RUN apt-get update && apt-get install -y --no-install-recommends \
            apt-utils \
            libcudnn7=$CUDNN_VERSION-1+cuda10.0 \
            libcudnn7-dev=$CUDNN_VERSION-1+cuda10.0 && \
    apt-mark hold libcudnn7 && \
    rm -rf /var/lib/apt/lists/*

# -------------------------------------------
# Anaconda installation
# https://github.com/ContinuumIO/docker-images/blob/master/anaconda3/debian/Dockerfile

ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV PATH /opt/conda/bin:$PATH

RUN apt-get update --fix-missing && \
    apt-get install -y wget bzip2 ca-certificates libglib2.0-0 libxext6 libsm6 libxrender1 git mercurial subversion && \
    apt-get clean

RUN wget --quiet https://repo.anaconda.com/archive/Anaconda3-2019.07-Linux-x86_64.sh -O ~/anaconda.sh && \
    /bin/bash ~/anaconda.sh -b -p /opt/conda && \
    rm ~/anaconda.sh && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc && \
    find /opt/conda/ -follow -type f -name '*.a' -delete && \
    find /opt/conda/ -follow -type f -name '*.js.map' -delete && \
    /opt/conda/bin/conda clean -afy

# install jupyterlab extensions
RUN conda config --add channels conda-forge
RUN	conda install -c conda-forge -y nodejs black jupyterlab_code_formatter
RUN jupyter labextension install @jupyterlab/toc
RUN jupyter labextension install @lckr/jupyterlab_variableinspector
RUN jupyter labextension install @ryantam626/jupyterlab_code_formatter

# for some other packages
RUN apt-get update --fix-missing && \
    apt-get install -y --no-install-recommends \
    unzip \
    zip \
    curl \
    tree

# get tini and installed
# tini is PID management software to avoid any abuse of PID in OS level
RUN apt-get install -y curl grep sed dpkg && \
    TINI_VERSION=`curl https://github.com/krallin/tini/releases/latest | grep -o "/v.*\"" | sed 's:^..\(.*\).$:\1:'` && \
    curl -L "https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini_${TINI_VERSION}.deb" > tini.deb && \
    dpkg -i tini.deb && \
    rm tini.deb && \
    apt-get clean

# pip installation
RUN curl "https://bootstrap.pypa.io/get-pip.py" -o "get-pip.py" && \
    python get-pip.py && \
    rm get-pip.py

# install dvc
RUN pip --no-cache-dir install \
    dvc

# Set up our notebook config.
COPY ./base/jupyter_notebook_config.py /root/.jupyter/

# Jupyter has issues with being run directly:
#   https://github.com/ipython/ipython/issues/7062
# We just add a little wrapper script.
COPY ./base/run_jupyter.sh /
RUN chmod +x /run_jupyter.sh

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