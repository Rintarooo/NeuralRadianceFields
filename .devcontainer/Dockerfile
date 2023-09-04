# https://pytorch.org/get-started/previous-versions/
ARG TORCH_VER=1.11.0
ARG TORCH_VISON_VER=0.12.0
ARG CUDA_VER=11.3.1
ARG UBUNTU_VER=20.04

# Ubuntu 20.04
# Python 3.9
# PyTorch 1.11.0
# torchvision 0.12.0
# PyTorch3D 0.6.2
# https://github.com/facebookresearch/pytorch3d/blob/2bd65027ca5c3b87b77d4f05b8eacae58d8d106f/.circleci/regenerate.py#L32
# Cuda 11.3.1

# check whether the base image exists in dockerhub repo
# https://hub.docker.com/r/nvidia/cuda
# FROM nvidia/cuda:${CUDA_VER}-base-ubuntu${UBUNTU_VER}
# FROM nvidia/cuda:${CUDA_VER}-cudnn8-runtime-ubuntu${UBUNTU_VER}
FROM nvidia/cuda:${CUDA_VER}-cudnn8-devel-ubuntu${UBUNTU_VER}


ARG DEBIAN_FRONTEND=noninteractive

ENV WORK_DIR /app
WORKDIR ${WORK_DIR}

ARG TZ=Asia/Tokyo
RUN ln -snf /usr/share/zoneinfo/${TZ} /etc/localtime && echo ${TZ} > /etc/timezone

# Install some basic utilities
RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    sudo \
    wget \
    curl \
    vim \
    git \
    bzip2 \
    build-essential \
    vim \
    libgl1-mesa-glx \
    x11-apps \
    libglib2.0-0 \
    zlib1g-dev \
    libffi-dev \
    libssl-dev \
    liblzma-dev \
    cmake \
    && apt-get autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# https://freak-da.hatenablog.com/entry/2020/03/31/094140
ARG TORCH_VER
ARG TORCH_VISON_VER
ARG UBUNTU_VER
ARG PYTHON_VER=3.9.0
ARG PYTHON_VER_MAJOR=39
ARG CUDA_VER_MAJOR=113
ARG TORCH3D_VER=0.6.2
ENV PYTHON_INSTALL_DIR /my_python/

# Install Python
RUN mkdir -p ${PYTHON_INSTALL_DIR} \
    &&  cd ${PYTHON_INSTALL_DIR} \
    &&  wget https://www.python.org/ftp/python/$PYTHON_VER/Python-$PYTHON_VER.tgz \
    &&  tar -xf Python-$PYTHON_VER.tgz \
    &&  rm Python-$PYTHON_VER.tgz \
    &&  cd Python-$PYTHON_VER/ \
    &&  ./configure --enable-optimizations \
    &&  make -j$(nproc) \
    &&  make install

RUN ln -fs ${PYTHON_INSTALL_DIR}/Python-${PYTHON_VER}/python /usr/bin/python \
    && curl -kL https://bootstrap.pypa.io/get-pip.py | python 


# RUN ln -s /usr/bin/python3 /usr/bin/python \
RUN pip3 install --upgrade pip setuptools wheel \
    &&  pip3 install torch==${TORCH_VER}+cu${CUDA_VER_MAJOR} torchvision==${TORCH_VISON_VER}+cu${CUDA_VER_MAJOR} --index-url https://download.pytorch.org/whl/cu${CUDA_VER_MAJOR} \
    &&  pip3 install opencv-python numpy tqdm ninja mediapipe jittor onnxruntime-gpu

# Install pytorch3d
# https://github.com/facebookresearch/pytorch3d/blob/main/INSTALL.md#3-install-wheels-for-linux
# https://github.com/facebookresearch/pytorch3d/tags
# torch3d:0.6.0, torch:1.9.0, cu111
# https://github.com/facebookresearch/pytorch3d/blob/2bd65027ca5c3b87b77d4f05b8eacae58d8d106f/.circleci/regenerate.py#L32
# torch3d:0.6.0, torch:1.11.0, cu113
# https://github.com/facebookresearch/pytorch3d/blob/9585a58d10cb2efcd159b058fa4af914203c1d0d/.circleci/regenerate.py#L29

# RUN echo $CUDA_HOME \
#     && export CUDA_HOME="/usr/local/cuda" \
# RUN export FORCE_CUDA="1" \


# RUN export CUDA_HOME="/usr/local/cuda" \
#     && export FORCE_CUDA="1" \

# ARG CUDA_HOME="/usr/local/cuda-11.3"
# RUN export FORCE_CUDA="1" \
#     && pip3 install "git+https://github.com/facebookresearch/pytorch3d.git@v${TORCH3D_VER}"

# RUN git clone https://github.com/facebookresearch/pytorch3d.git \
#     && cd pytorch3d \
#     && git checkout -q v${TORCH3D_VER}\
#     && pip install -e .

RUN pip install "git+https://github.com/facebookresearch/pytorch3d.git@v${TORCH3D_VER}"

# RUN pip install --no-index --no-cache-dir pytorch3d -f https://dl.fbaipublicfiles.com/pytorch3d/packaging/wheels/py${PYTHON_VER_MAJOR}_cu${CUDA_VER_MAJOR}_pyt${CUDA_VER_MAJOR}1/download.html
# pip3 install --no-index --no-cache-dir pytorch3d -f https://dl.fbaipublicfiles.com/pytorch3d/packaging/wheels/py38_cu${CUDA_VER_MAJOR}_pyt1110/download.html

# https://stackoverflow.com/questions/71470989/python-setup-py-bdist-wheel-did-not-run-successfully
RUN pip install hydra-core omegaconf imageio 
# hydra
# wheel cmake 


# https://qiita.com/Spritaro/items/602118d946a4383bd2bb
# ローカルuser作成
ARG USER_NAME=user
ARG GROUP_NAME=user
ARG UID=1000
ARG GID=1000
ARG PIN=password
RUN groupadd -g $GID $GROUP_NAME \
    && useradd -m -s /bin/bash -u $UID -g $GID -G sudo $USER_NAME \
    && gpasswd -a $USER_NAME sudo \
    && echo "${USER_NAME}:${PIN}" | chpasswd \
    && echo "${USER_NAME} ALL=(ALL) ALL" >> /etc/sudoers \
    && chmod g+w /etc/passwd
USER $USER_NAME
