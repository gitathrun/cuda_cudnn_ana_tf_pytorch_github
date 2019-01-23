# for a docker image with:
# CUDA 9.0 + cuDNN 7.x
# Anaconda 5.2 (python 3.6)
# PyTorch 1.0
# gpu-pytorch:latest
# For tag and push



# FROM cuda_cudnn_ana_base:9.0-7.2-5.3
FROM tftwdockerhub/cuda_cudnn_ana_base:latest

# the maintainer information
LABEL maintainer "Teng Fu <teng.fu@teleware.com>"

# previous run
# RUN conda install pytorch torchvision magma-cuda90 cuda90 -c pytorch && conda clean -ya
RUN conda install pytorch torchvision -c pytorch && conda clean -ya

ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES compute,utility
ENV LD_LIBRARY_PATH /usr/local/nvidia/lib:/usr/local/nvidia/lib64

WORKDIR /app