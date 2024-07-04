FROM ubuntu:22.04
ENV TZ=Asia/Shanghai
RUN apt update && apt install -y python3-pip && rm -rf /var/lib/apt/lists/*
RUN python3 -m pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
RUN python3 -m pip install --no-cache tqdm requests flask xlrd xlwt openpyxl confluent_kafka
RUN python3 -m pip install --no-cache torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
RUN python3 -m pip install --no-cache onnxruntime-gpu
RUN python3 -m pip install --no-cache ultralytics==8.2.1
WORKDIR /workspace
