# syntax=docker/dockerfile:1.4

# First stage: Builder
FROM nvidia/cuda:12.6.1-cudnn-runtime-ubuntu20.04 AS builder

WORKDIR /annotation_service_modeling

ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=UTC

# Install Python 3.8 and pip
RUN apt-get update && \
    apt-get install -y python3.8 python3-pip && \
    ln -s /usr/bin/python3.8 /usr/bin/python

# Install necessary build packages
RUN apt-get install -y build-essential ffmpeg libsm6 libxext6 git

COPY requirements.txt /annotation_service_modeling

# Build wheels for PyTorch with CUDA support with caching
RUN --mount=type=cache,target=/root/.cache/pip \
    python -m pip wheel --wheel-dir=/wheels torch==1.4.0 torchvision==0.5.0 -f https://download.pytorch.org/whl/cu101/torch_stable.html

# Build wheels for requirements with caching
RUN --mount=type=cache,target=/root/.cache/pip \
    python -m pip wheel --wheel-dir=/wheels -r requirements.txt

# Build wheel for Detectron2 with CUDA support with caching
RUN --mount=type=cache,target=/root/.cache/pip \
    python -m pip wheel --wheel-dir=/wheels detectron2==0.1.1 -f https://dl.fbaipublicfiles.com/detectron2/wheels/cu101/torch1.4/index.html

# Build wheel for label-studio from GitHub with caching
RUN --mount=type=cache,target=/root/.cache/pip \
    python -m pip wheel --wheel-dir=/wheels git+https://github.com/Layout-Parser/label-studio.git

# Second stage: Final image
FROM nvidia/cuda:12.6.1-cudnn-runtime-ubuntu20.04

WORKDIR /annotation_service_modeling

ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=UTC

# Install Python 3.8 and pip
RUN apt-get update && \
    apt-get install -y python3.8 python3-pip && \
    ln -s /usr/bin/python3.8 /usr/bin/python

# Install only necessary runtime packages
RUN apt-get install -y ffmpeg libsm6 libxext6 git

COPY requirements.txt /annotation_service_modeling

# Copy wheels from the builder stage
COPY --from=builder /wheels /wheels

# Install wheels
RUN python -m pip install --no-index --find-links=/wheels torch torchvision detectron2 label-studio && \
    python -m pip install --no-index --find-links=/wheels -r requirements.txt

COPY tools /annotation_service_modeling/tools

EXPOSE 9090
