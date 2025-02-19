# Use Ubuntu as base image
FROM ubuntu:20.04

# Install necessary dependencies
RUN apt-get update && \
    apt-get install -y \
    curl \
    git \
    unzip \
    protobuf-compiler \
    build-essential \
    python3-dev \
    python3-pip \
    && rm -rf /var/lib/apt/lists/*

# Clone the TensorFlow Models repository
RUN git clone --depth 1 https://github.com/tensorflow/models.git

# Download and install protoc 3.13.0 for AMD64
WORKDIR /tmp
RUN curl -OL https://github.com/protocolbuffers/protobuf/releases/download/v3.13.0/protoc-3.13.0-linux-x86_64.zip && \
    unzip protoc-3.13.0-linux-x86_64.zip -d /usr/local && \
    rm protoc-3.13.0-linux-x86_64.zip

# Verify installation
RUN protoc --version

# Change to root directory
WORKDIR /

# Install Cython and clone COCO API repository
RUN pip install cython && \
    git clone --depth 1 https://github.com/cocodataset/cocoapi.git

# Compile COCO API and copy pycocotools to models/research
WORKDIR /cocoapi/PythonAPI/
RUN make && cp -r pycocotools /models/research/
