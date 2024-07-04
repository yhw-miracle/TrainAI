FROM nvcr.io/nvidia/tensorrt:23.10-py3
ENV TZ=Asia/Shanghai
# install python3-pip
RUN apt update && apt install -y python3-pip && pip3 install --upgrade pip && pip3 install numpy
# add CUDA lib PATH
RUN CUDA_PATH=(/usr/local/cuda-*) && \
    CUDA=`basename $CUDA_PATH` && \
    echo "$CUDA_PATH/compat" >> /etc/ld.so.conf.d/${CUDA/./-}.conf && \
    ldconfig
# install all dependencies for OpenCV
RUN apt install --no-install-recommends -y \
    unzip \
    cmake \
    ffmpeg \
    libtbb12 \
    gfortran \
    apt-utils \
    pkg-config \
    checkinstall \
    qtbase5-dev qt5-qmake \
    build-essential \
    libopenblas-base \
    libopenblas-dev \
    liblapack-dev \
    libatlas-base-dev \
    libavcodec-dev \
    libavformat-dev \
    libavutil-dev \
    libswscale-dev \
    libjpeg8-dev \
    libpng-dev \
    libtiff5-dev \
    libxine2-dev \
    libv4l-dev \
    libgstreamer1.0-0 \
    libgstreamer1.0-dev \
    libgstreamer-plugins-base1.0-0 \
    libgstreamer-plugins-base1.0-dev \
    libgstreamer1.0-dev \
    libgstreamer-plugins-good1.0-0 \
    libgstreamer-plugins-base1.0-0 \
    libgstreamer-plugins-bad1.0-0 \
    gstreamer1.0-tools \
    gstreamer1.0-vaapi \
    gstreamer1.0-rtsp \
    gstreamer1.0-plugins-bad  \
    gstreamer1.0-plugins-good  \
    gstreamer1.0-plugins-ugly \
    gstreamer1.0-plugins-rtp \
    gstreamer1.0-libav \
    gstreamer1.0-tools \
    gstreamer1.0-x \
    libglew-dev \
    libpostproc-dev \
    libeigen3-dev \
    libtbb-dev \
    zlib1g-dev \
    libsm6 \
    libxext6 \
    libxrender1 \
    libswresample-dev \
    libdc1394-dev \
    libfreetype-dev \
    libharfbuzz-dev \
    libtesseract-dev \
    libtesseract4 \
    git \
    zip \
    unzip \
    curl \
    htop \
    libgl1-mesa-glx \
    libglib2.0-0 \
    libpython3-dev \
    gnupg \
    g++ \
    libturbojpeg \
    vim \
    axel \
    wget \
    curl
# download OpenCV source
ARG OPENCV_VERSION=4.x
RUN axel https://github.com/opencv/opencv/archive/refs/heads/${OPENCV_VERSION}.zip -o opencv.zip && unzip opencv.zip && rm -v opencv.zip && mv opencv-${OPENCV_VERSION} /opencv
RUN axel https://github.com/opencv/opencv_contrib/archive/refs/heads/${OPENCV_VERSION}.zip -o opencv_contrib.zip && unzip opencv_contrib.zip && rm -v opencv_contrib.zip && mv opencv_contrib-${OPENCV_VERSION} /opencv_contrib

# install Nvidia video codec
RUN wget https://github.com/yhw-miracle/TrainAI/releases/download/video_codec_sdk/Video_Codec_SDK_12.1.14.zip -O /workspace/Video_Codec_SDK_12.1.14.zip --progress=bar:force:noscroll --no-check-certificate && unzip /workspace/Video_Codec_SDK_12.1.14.zip -d /workspace/ && rm -v /workspace/Video_Codec_SDK_12.1.14.zip && cd /workspace/Video_Codec_SDK_12.1.14 && cp Interface/* /usr/local/cuda/targets/x86_64-linux/include/ && cp Lib/linux/stubs/x86_64/* /usr/local/cuda/targets/x86_64-linux/lib/stubs/ && cd /usr/local/cuda/targets/x86_64-linux/lib/stubs/ && ln -s libcuda.so libcuda.so.1 && ln -s libnvcuvid.so libnvcuvid.so.1 && ln -s libnvidia-encode.so libnvidia-encode.so.1

# prepare build
ARG PYTHON_VERSION=3.10
RUN mkdir /opencv/build && cd /opencv/build && cmake \
    -D CMAKE_BUILD_TYPE=RELEASE \
    -D BUILD_PYTHON_SUPPORT=ON \
    -D BUILD_DOCS=ON \
    -D BUILD_PERF_TESTS=OFF \
    -D BUILD_TESTS=OFF \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D OPENCV_EXTRA_MODULES_PATH=/opencv_contrib/modules \
    -D BUILD_opencv_python3=$( [ ${PYTHON_VERSION%%.*} -ge 3 ] && echo "ON" || echo "OFF" ) \
    -D BUILD_opencv_python2=$( [ ${PYTHON_VERSION%%.*} -lt 3 ] && echo "ON" || echo "OFF" ) \
    -D PYTHON${PYTHON_VERSION%%.*}_EXECUTABLE=$(which python${PYTHON_VERSION}) \
    -D PYTHON_DEFAULT_EXECUTABLE=$(which python${PYTHON_VERSION}) \
    -D BUILD_EXAMPLES=OFF \
    -D WITH_IPP=OFF \
    -D WITH_FFMPEG=ON \
    -D WITH_GSTREAMER=ON \
    -D WITH_V4L=ON \
    -D WITH_LIBV4L=ON \
    -D WITH_TBB=ON \
    -D WITH_QT=ON \
    -D WITH_OPENGL=ON \
    -D WITH_CUDA=ON \
    -D WITH_CUDNN=ON \
    -D BUILD_CUDA_STUBS=ON \
    -D OPENCV_DNN_CUDA=ON \
    -D WITH_LAPACK=ON \
    #-D WITH_HPX=ON \
    -D CUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda \
    -D CMAKE_LIBRARY_PATH=/usr/local/cuda/lib64/stubs \
    # https://kezunlin.me/post/6580691f
    # https://stackoverflow.com/questions/28010399/build-opencv-with-cuda-support
    -D CUDA_ARCH_BIN="6.1 7.0 7.5 8.0 8.6 8.9 9.0" \
    -D CUDA_ARCH_PTX="" \
    -D WITH_CUBLAS=ON \
    -D WITH_NVCUVID=ON \
    -D ENABLE_FAST_MATH=1 \
    -D CUDA_FAST_MATH=1 \
    -D BUILD_opencv_cudaoptflow=ON \
    -D BUILD_opencv_cudacodec=ON \
    -D BUILD_opencv_cudev=ON \
    -D BUILD_opencv_cudaarithm=ON \
    -D BUILD_opencv_cudafeatures2d=ON \
    -D BUILD_opencv_cudafilters=ON \
    -D BUILD_opencv_cudaimgproc=ON \
    -D BUILD_opencv_cudaobjdetect=ON \
    -D BUILD_opencv_cudastereo=ON \
    -D BUILD_opencv_cudawarping=ON \
    -D BUILD_opencv_cudacnn=ON \
    -D BUILD_opencv_cudabgsegm=ON \
    -D BUILD_opencv_cudastitching=ON \
    # -D BUILD_opencv_world=OFF \
    -D ENABLE_PRECOMPILED_HEADERS=OFF \
    -D OPENCV_ENABLE_NONFREE=ON \
    ..

# build, text and install
RUN cd /opencv/build && make -j$(nproc) && make install && ldconfig
# gdal
RUN apt install -y gdal-bin python3-gdal
# clean
RUN rm -rf /opencv /opencv_contrib /var/lib/apt/lists/*
# install python packages
RUN pip3 install --no-cache tritonclient[all] tqdm pyturbojpeg pika scipy PyYAML matplotlib requests flask minio lapx ffmpeg-python xlrd xlwt openpyxl confluent_kafka
ENV GST_PLUGIN_FEATURE_RANK="nvcodec:256,nvh264dec:256,nvh265dec:256,nvvp9dec:256,nvvp8dec:256,nvh264enc:256,nvh265enc:256,avdec_h265:128,avdec_h264:128,avdec_vp9:128,avdec_vp8:128,avenc_h264_omx:128"