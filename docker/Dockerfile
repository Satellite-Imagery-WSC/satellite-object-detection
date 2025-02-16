# Use Ubuntu 18.04 to maintain compatibility with the original instructions
FROM ubuntu:18.04

# Avoid interactive prompts and set LD_LIBRARY_PATH for CUDA
ENV DEBIAN_FRONTEND=noninteractive
ENV LD_LIBRARY_PATH=/usr/local/cuda-11.0/lib64:$LD_LIBRARY_PATH

# Install required system dependencies
RUN apt-get update && apt-get install -y \
    git \
    wget \
    unzip \
    curl \
    sudo \
    python3 \
    python3-pip \
    python3-dev \
    protobuf-compiler \
    && rm -rf /var/lib/apt/lists/*

# Create an alias to use 'python' instead of 'python3'
RUN ln -s /usr/bin/python3 /usr/bin/python

# Uninstall Cython (temporary fix for "No module named 'object_detection'" error)
RUN pip3 uninstall -y Cython || true

# Clone the TensorFlow Models repository from GitHub
RUN git clone --depth 1 https://github.com/tensorflow/models /models

# Change to the research directory and compile protobuf files
WORKDIR /models/research
RUN protoc object_detection/protos/*.proto --python_out=.

# Copy and modify setup.py to force tf-models-official version to 2.8.0
RUN cp /models/research/object_detection/packages/tf2/setup.py /models/research/setup.py
RUN sed -i 's/tf-models-official>=2.5.1/tf-models-official==2.8.0/' /models/research/setup.py

# Install PyYAML with the required version (temporary fix for Colab)
RUN pip3 install pyyaml==5.3

# Install the Object Detection API (packages and installs everything from /models/research/)
RUN echo "Current directory:" $(pwd) && pip3 install /models/research/

# # Install TensorFlow 2.8.0 and tensorflow_io (downgrade for compatibility)
# RUN pip3 install tensorflow==2.8.0 tensorflow_io==0.23.1

# # Install CUDA version 11.0 (to maintain compatibility with TF 2.8.0)
# # Download and move CUDA configuration file
# RUN wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin && \
#     mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
# # Download the local installer for CUDA 11.0.2
# RUN wget http://developer.download.nvidia.com/compute/cuda/11.0.2/local_installers/cuda-repo-ubuntu1804-11-0-local_11.0.2-450.51.05-1_amd64.deb
# # Install the CUDA .deb package
# RUN dpkg -i cuda-repo-ubuntu1804-11-0-local_11.0.2-450.51.05-1_amd64.deb
# # Add the public key for the CUDA repository
# RUN apt-key add /var/cuda-repo-ubuntu1804-11-0-local/7fa2af80.pub
# # Update and install cuda-toolkit-11-0
# RUN apt-get update && apt-get install -y cuda-toolkit-11-0

# # Configure protoc and protobuf to the correct versions
# RUN apt-get remove -y protobuf-compiler
# RUN pip3 install 'protobuf<=3.20.1' --force-reinstall
# RUN wget https://github.com/protocolbuffers/protobuf/releases/download/v3.20.1/protoc-3.20.1-linux-x86_64.zip
# RUN unzip protoc-3.20.1-linux-x86_64.zip -d protoc3
# RUN mv protoc3/bin/* /usr/local/bin/
# RUN mv protoc3/include/* /usr/local/include

# Return to the root directory
WORKDIR /

# Default command: start a bash session
CMD ["/bin/bash"]
