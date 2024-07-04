FROM yhwmiracle/inference:v1.2
# install yolo train python packages
RUN pip3 install --no-cache torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
RUN pip3 install --no-cache onnxruntime-gpu
RUN pip3 install --no-cache ultralytics==8.2.1