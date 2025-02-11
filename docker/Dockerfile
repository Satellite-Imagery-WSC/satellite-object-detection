# Se utiliza una imagen base de Python (versión 3.8 como ejemplo)
FROM python:3.8-slim

# Definimos variables de entorno para minimizar los mensajes de logging
ENV TF_CPP_MIN_LOG_LEVEL=2

# Actualizamos lista de paquetes e instalamos las herramientas necesarias
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        git \
        protobuf-compiler \
        python3-dev \
        build-essential \
        wget \
        curl \
    && rm -rf /var/lib/apt/lists/*

# Instalamos la última versión de pip
RUN pip install --upgrade pip

# Instalamos TensorFlow (ajusta la versión según tus necesidades)
RUN pip install --no-cache-dir tensorflow==2.10.0

# Clonamos el repositorio de modelos de TensorFlow (contiene la API de Object Detection)
RUN git clone https://github.com/tensorflow/models.git /models

# Instalamos las dependencias específicas de la API de Object Detection
# (Esta sección puede adaptarse si tienes requerimientos adicionales)
WORKDIR /models/research
RUN pip install --no-cache-dir cython contextlib2 pillow lxml jupyter matplotlib

# Compilamos los ficheros protobuf para la API de Object Detection
RUN protoc object_detection/protos/*.proto --python_out=.

# Instalamos la librería "pycocotools" (algunas funciones requieren COCO)
RUN pip install --no-cache-dir pycocotools

# Instalamos la API de Object Detection como paquete
RUN cp object_detection/packages/tf2/setup.py . && \
    pip install --no-cache-dir .

# (Opcional) Verificamos la instalación ejecutando los tests (recomendado, aunque más lento)
# RUN python -m unittest discover -s object_detection -p "*_test.py"

# Añadimos el repositorio "research/slim" al PYTHONPATH (opcional)
ENV PYTHONPATH=$PYTHONPATH:/models/research:/models/research/slim

# Establecemos el directorio de trabajo por defecto
WORKDIR /workspace

# Ejemplo de comando por defecto: bash interactivo
CMD ["/bin/bash"]