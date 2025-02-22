# Use Ubuntu as the base image
FROM ubuntu:20.04

ENV DEBIAN_FRONTEND=noninteractive

# Install necessary dependencies
RUN apt-get update && \
    apt-get install -y \
    curl \
    git \
    unzip \
    protobuf-compiler \
    build-essential \
    python3 \
    python3-dev \
    python3-pip \
    libgl1 \
    libglib2.0-0 \
    && rm -rf /var/lib/apt/lists/*

# Create an alias for python from python3
RUN ln -s /usr/bin/python3 /usr/bin/python

# Install TensorFlow 2.*
RUN python3 -m pip install --no-cache-dir "tensorflow==2.13.0"

RUN python3 -m pip install --upgrade pip setuptools setuptools-scm==7.1.0

# Install TensorFlow, TensorFlow-Text, TensorFlow-Models-Official and pyglib
RUN python3 -m pip install \
    tensorflow-text==2.13.0 \
    tensorflow-model-optimization==0.8.0 \
    tf-models-official==2.13.1 \
    pyglib

# Clone the TensorFlow Models repository (Commit on Feb 18, 2025)
RUN git clone --branch master \
    https://github.com/tensorflow/models.git && \
    cd /models && \
    git checkout 4a56eff0ad5ce23f64f7638167c3256b36da3b07

# Download and install protoc 3.19.0 for AMD64 architecture
WORKDIR /tmp
RUN curl -OL https://github.com/protocolbuffers/protobuf/releases/download/v3.19.0/protoc-3.19.0-linux-x86_64.zip && \
    unzip protoc-3.19.0-linux-x86_64.zip -d /usr/local && \
    rm protoc-3.19.0-linux-x86_64.zip

# Change to the TensorFlow models/research directory
WORKDIR /models/research

# Compile the protobuf files (.proto) to generate Python files using the correct path for protoc
RUN /usr/local/bin/protoc object_detection/protos/*.proto --python_out=.

# Change to the root directory
WORKDIR /

# Install Cython and clone the COCO API repository (Commit on Feb 20, 2020)
RUN pip install cython==3.0.12 && \
    git clone --branch  master\
    https://github.com/cocodataset/cocoapi.git && \
    cd /cocoapi && \
    git checkout 8c9bcc3cf640524c4c20a9c40e89cb6a2f2fa0e9

# Compile COCO API and copy pycocotools to the models/research directory
WORKDIR /cocoapi/PythonAPI/
RUN make && cp -r pycocotools /models/research/

# Define the working directory where 'object_detection/packages/tf2/setup.py' is located
WORKDIR /models/research

# Copy the setup.py file to the current directory
RUN cp object_detection/packages/tf2/setup.py . 

# Install the package using pip
RUN python3 -m pip install .

# Run the model test
RUN python3 object_detection/builders/model_builder_tf2_test.py