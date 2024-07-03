FROM ubuntu:22.04

ARG PYTHON_VERSION=3.10

SHELL ["/bin/bash", "-c"]
ENV DEBIAN_FRONTEND=noninteractive TZ=Asia/Shanghai

RUN apt-get update -qq --fix-missing && \
    apt-get install -y --no-install-recommends software-properties-common && \
    if [[ "$PYTHON_VERSION" =~ ^(2.7|3.6|3.7)$ ]]; then \
        add-apt-repository ppa:deadsnakes/ppa ; \
    fi && \
    apt-add-repository universe && \
    apt-get -y update -qq --fix-missing && \
    apt-get -y install --no-install-recommends \
        python${PYTHON_VERSION} \
        python${PYTHON_VERSION}-dev \
        python3-pip \
        python-is-python3 \
        $( [ ${PYTHON_VERSION%%.*} -ge 3 ] && echo "python${PYTHON_VERSION}-distutils" ) \
        curl \
        wget \
    && \
    rm -rf /var/lib/apt/lists/*

RUN python3 -m pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
RUN python3 -m pip install --upgrade pip wheel
RUN python3 -m pip install --no-cache tqdm requests flask xlrd xlwt openpyxl confluent_kafka
RUN python3 -m pip install --no-cache torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
RUN python3 -m pip install --no-cache onnxruntime-gpu --extra-index-url https://aiinfra.pkgs.visualstudio.com/PublicPackages/_packaging/onnxruntime-cuda-12/pypi/simple/
RUN python3 -m pip install --no-cache ultralytics==8.2.1
WORKDIR /workspace
