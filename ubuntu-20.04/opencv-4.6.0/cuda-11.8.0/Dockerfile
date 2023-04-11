ARG CUDA_VERSION=11.8.0
ARG CUDNN_VERSION=8
ARG UBUNTU_VERSION=20.04

FROM nvidia/cuda:${CUDA_VERSION}-cudnn${CUDNN_VERSION}-devel-ubuntu${UBUNTU_VERSION}

ARG PYTHON_VERSION=3.8
ARG OPENCV_VERSION=4.6.0
ARG DEBIAN_FRONTEND=noninteractive

# nvidia-container-runtime
# ENV NVIDIA_VISIBLE_DEVICES \
#     ${NVIDIA_VISIBLE_DEVICES:-all}
# ENV NVIDIA_DRIVER_CAPABILITIES \
#     ${NVIDIA_DRIVER_CAPABILITIES:+$NVIDIA_DRIVER_CAPABILITIES,}graphics

RUN apt-get update -y && apt-get upgrade -y \
    # Install build tools, build dependencies and python
    && apt-get install -y --no-install-recommends \
    wget \
    gnupg \
    python3-pip \
    build-essential \
    cmake \
    git \
    unzip \
    yasm \
    pkg-config \
    libswscale-dev \
    libtbb2 \
    libtbb-dev \
    libjpeg-dev \
    libpng-dev \
    libtiff-dev \
    libavformat-dev \
    libpq-dev \
    libxine2-dev \
    libglew-dev \
    libtiff5-dev \
    zlib1g-dev \
    libjpeg-dev \
    libavcodec-dev \
    libavformat-dev \
    libavutil-dev \
    libpostproc-dev \
    libswscale-dev \
    libeigen3-dev \
    libtbb-dev \
    libgtk2.0-dev \
    pkg-config \
    ## Python
    python3-dev \
    python3-numpy \
    build-essential \
    && apt-get -y update --fix-missing \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*


RUN cd /opt/ &&\
    # Download and unzip OpenCV and opencv_contrib and delte zip files
    wget https://github.com/opencv/opencv/archive/${OPENCV_VERSION}.zip &&\
    unzip ${OPENCV_VERSION}.zip &&\
    rm ${OPENCV_VERSION}.zip &&\
    wget https://github.com/opencv/opencv_contrib/archive/${OPENCV_VERSION}.zip &&\
    unzip ${OPENCV_VERSION}.zip &&\
    rm ${OPENCV_VERSION}.zip &&\
    # Create build folder and switch to it
    mkdir /opt/opencv-${OPENCV_VERSION}/build && cd /opt/opencv-${OPENCV_VERSION}/build &&\
    # Cmake configure
    cmake \
        -DWITH_CUDA=ON \
        -D BUILD_opencv_dnn=ON \
        -DCUDA_ARCH_BIN=7.5,8.0,8.6 \
        -D WITH_CUBLAS=ON \
        -D WITH_CUDNN=ON \
        -D OPENCV_DNN_CUDA=ON \
        -D ENABLE_FAST_MATH=1\
        -D CUDA_FAST_MATH=1\
        -D WITH_NVCUVID=ON \
        -D WITH_CUFFT=ON \
        -D WITH_OPENGL=ON \
        -D WITH_QT=ON \
        -D WITH_IPP=ON \
        -D WITH_TBB=ON \
        -D WITH_EIGEN=ON \
        -D CUDA_NVCC_FLAGS=-Wno-deprecated-gpu-targets \
        -D CMAKE_BUILD_TYPE=RELEASE \
        -D OPENCV_EXTRA_MODULES_PATH=/opt/opencv_contrib-${OPENCV_VERSION}/modules \
        -D PYTHON_EXECUTABLE=$(which python${PYTHON_VERSION}) \
        # -D PYTHON3_PACKAGES_PATH=/usr/lib/python${PYTHON_VERSION}/site-packages \
        # Install path will be /usr/local/lib (lib is implicit)
        -DCMAKE_INSTALL_PREFIX=/usr/local \
        .. &&\
    # # Make
    make -j"$(nproc)" &&\
    # # Install to /usr/local/lib
    make install &&\
    ldconfig &&\
    # # Remove OpenCV sources and build folder
    rm -rf /opt/opencv-${OPENCV_VERSION} && rm -rf /opt/opencv_contrib-${OPENCV_VERSION}

RUN echo "PYTHONPATH=$PYTHONPATH:/usr/local/lib/python3.8/site-packages" >> /root/.bashrc &&\
    echo "export PYTHONPATH" >> /root/.bashrc