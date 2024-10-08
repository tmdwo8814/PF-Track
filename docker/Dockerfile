# Base image
ARG PYTORCH="1.9.0"
ARG CUDA="11.1"
ARG CUDNN="8"

FROM pytorch/pytorch:${PYTORCH}-cuda${CUDA}-cudnn${CUDNN}-devel

ENV TORCH_CUDA_ARCH_LIST="6.0 6.1 7.0+PTX"
ENV TORCH_NVCC_FLAGS="-Xfatbin -compress-all"
ENV CMAKE_PREFIX_PATH="$(dirname $(which conda))/../"

# To fix GPG key error when running apt-get update
RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/3bf863cc.pub
RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/7fa2af80.pub

# Core tools
RUN apt-get update && apt-get install -y \
    cmake \
    curl \
    docker.io \
    ffmpeg \
    git \
    htop \
    libsm6 \
    libxext6 \
    libglib2.0-0 \
    libsm6 \
    libxrender-dev \
    libxext6 \
    ninja-build \
    unzip \
    vim \
    wget \
    sudo \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*


# 필수 도구 설치 및 software-properties-common 설치
RUN apt-get update && apt-get install -y \
    build-essential \
    cmake \
    curl \
    docker.io \
    ffmpeg \
    git \
    htop \
    libsm6 \
    libxext6 \
    libglib2.0-0 \
    libxrender-dev \
    ninja-build \
    unzip \
    vim \
    wget \
    sudo \
    software-properties-common \  
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*


RUN add-apt-repository ppa:deadsnakes/ppa && \
    apt-get update && \
    apt-get install -y python3.8 python3.8-dev python3.8-distutils && \
    curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py && \
    python3.8 get-pip.py && \
    rm get-pip.py

# Python 명령어를 python3.8로 심볼릭 링크 설정
RUN ln -sf /usr/bin/python3.8 /usr/bin/python && \
    ln -sf /usr/bin/python3.8 /usr/local/bin/python && \
    rm -f /usr/local/bin/pip && ln -s /usr/local/bin/pip3 /usr/local/bin/pip


# Allow OpenSSH to talk to containers without asking for confirmation
RUN cat /etc/ssh/ssh_config | grep -v StrictHostKeyChecking > /etc/ssh/ssh_config.new && \
    echo "    StrictHostKeyChecking no" >> /etc/ssh/ssh_config.new && \
    mv /etc/ssh/ssh_config.new /etc/ssh/ssh_config


# Python tools
RUN pip install \
    awscli==1.20.27 \
    boto3==1.18.27 \
    coloredlogs==15.0.1 \
    gdown \
    gpustat \
    hydra-core==1.1.1 \
    omegaconf \
    seaborn==0.11.2 \
    wandb==0.12.17 \
    # dataset specific tools
    # pycocotools==2.0.4 \
    nuscenes-devkit==1.1.7 \
    motmetrics==1.1.3

# Install OpenMMLab packages
ARG MMCV="1.4.0"
ARG MMDET="v2.24.1"
ARG MMSEG="v0.20.2"
ARG MMDET3D="v0.17.1"
ENV FORCE_CUDA="1"

# Optional: SimpleTrack for BEV visualization
# RUN git clone https://github.com/tusen-ai/SimpleTrack && cd SimpleTrack && \
#     pip install -e .

RUN pip install mmcv-full==${MMCV} -f https://download.openmmlab.com/mmcv/dist/cu111/torch1.9.0/index.html

WORKDIR /workspace/mmlab
RUN git clone https://github.com/open-mmlab/mmdetection.git && cd mmdetection && \
    git checkout ${MMDET} && \
    pip install -r requirements/build.txt && pip install -e .
ENV PYTHONPATH="/workspace/mmlab/mmdetection:$PYTHONPATH"

RUN git clone https://github.com/open-mmlab/mmsegmentation.git && cd mmsegmentation && \
    git checkout ${MMSEG} && \
    pip install -e .
ENV PYTHONPATH="/workspace/mmlab/mmsegmentation:$PYTHONPATH"

# RUN git clone https://github.com/open-mmlab/mmdetection3d.git && cd mmdetection3d && \
#     # git checkout ${MMDET3D} && \
#     pip install -e .
# ENV PYTHONPATH="/workspace/mmlab/mmdetection3d:$PYTHONPATH"

WORKDIR /workspace/PF_Track
RUN git config --global --add safe.directory /workspace/PF_Track
