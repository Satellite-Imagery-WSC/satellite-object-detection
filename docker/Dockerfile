# Usa una imagen base de TensorFlow
FROM tensorflow/tensorflow:2.3.0-gpu

# Instalar dependencias del sistema
RUN apt-get update && \
    apt-get install -y \
    git \
    unzip \
    protobuf-compiler \
    build-essential \
    python3-dev \
    python3-pip \
    cython

# Crear y activar el entorno virtual de TensorFlow
RUN python3 -m venv /tensorflow/tf2_api_env
ENV PATH="/tensorflow/tf2_api_env/bin:$PATH"

# Instalar TensorFlow
RUN pip install tensorflow==2.*

# Clonar el repositorio de TensorFlow Models
RUN git clone --recurse-submodules https://github.com/tensorflow/models.git

# Establecer el directorio correcto para compilar los archivos .proto
WORKDIR /tensorflow/models/research

# Compilar los archivos .proto
RUN protoc object_detection/protos/*.proto --python_out=.

# Instalar la API COCO
RUN pip install cython
RUN git clone https://github.com/cocodataset/cocoapi.git
WORKDIR /tensorflow/models/research/cocoapi/PythonAPI
RUN make
RUN cp -r pycocotools /tensorflow/models/research/

# Instalar la API de Object Detection
WORKDIR /tensorflow/models/research
RUN cp object_detection/packages/tf2/setup.py .
RUN python -m pip install .

# Realizar limpieza de dependencias no necesarias
RUN apt-get purge -y --auto-remove git unzip build-essential python3-dev
