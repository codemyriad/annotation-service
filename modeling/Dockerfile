FROM nvidia/cuda:12.6.1-cudnn-runtime-ubuntu20.04

WORKDIR /annotation_service_modeling

# Install Python 3.8 and pip
RUN apt-get update && \
    apt-get install -y python3.8 && \
    ln -s /usr/bin/python3.8 /usr/bin/python


ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=UTC

# Install necessary packages
RUN apt-get install -y build-essential ffmpeg libsm6 libxext6 git

RUN apt install python3-pip -y

COPY requirements.txt /annotation_service_modeling

# Install PyTorch with CUDA support
RUN python3.8 -m pip install torch==1.4.0 torchvision==0.5.0 -f https://download.pytorch.org/whl/cu101/torch_stable.html

RUN python3.8 -m pip install -r requirements.txt

# Install Detectron2 with CUDA support
RUN python3.8 -m pip install detectron2==0.1.1 -f https://dl.fbaipublicfiles.com/detectron2/wheels/cu101/torch1.4/index.html

RUN python3.8 -m pip install git+https://github.com/Layout-Parser/label-studio.git

COPY tools /annotation_service_modeling/tools

EXPOSE 9090
