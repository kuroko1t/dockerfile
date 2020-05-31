FROM kuroko1t/jetson:build

RUN wget https://nvidia.box.com/shared/static/3ibazbiwtkl181n95n9em3wtrca7tdzp.whl -O torch-1.5.0-cp36-cp36m-linux_aarch64.whl

RUN wget https://github.com/opencv/opencv/archive/4.3.0.tar.gz && \
    tar zxvf 4.3.0.tar.gz && rm 4.3.0.tar.gz && \
    wget https://github.com/opencv/opencv_contrib/archive/4.3.0.tar.gz && \
    tar zxvf 4.3.0.tar.gz && rm 4.3.0.tar.gz

RUN cd opencv-4.3.0 && mkdir release && cd release && \
    cmake -D CUDA_HOST_COMPILER=/usr/bin/gcc-7 -D CUDA_NVCC_FLAGS="--expt-relaxed-constexpr" -DWITH_NVCUVID=OFF -DENABLE_CCACHE=OFF -DEIGEN_INCLUDE_PATH=/usr/include/eigen3 \
    -DWITH_FAST_MATH=OFF -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib-4.3.0/modules -D WITH_GSTREAMER=ON -D WITH_LIBV4L=ON -D BUILD_opencv_python2=OFF \
    -D BUILD_opencv_python3=ON -DPYTHON3_EXECUTABLE=/usr/bin/python3 -D BUILD_TESTS=OFF -D BUILD_PERF_TESTS=OFF -D BUILD_EXAMPLES=OFF -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local/ -D LAPACK_LIBRARIES=/usr/lib/aarch64-linux-gnu/libblas.so -D LAPACK_CBLAS_H=/usr/include/aarch64-linux-gnu/cblas.h .. &&\
    make -j 3 && make install

RUN pip3 install -U pip && pip3 install wheel setuptools Cython

RUN pip3 wheel pycuda

RUN pip3 wheel grpcio

RUN pip3 wheel onnx

RUN git clone https://github.com/kuroko1t/cv2.git && \
    mv /usr/local/lib/python3.6/dist-packages/cv2/python-3.6/cv2.cpython-36m-aarch64-linux-gnu.so cv2/cv2/_native/ && \
    cd cv2 && python3 setup.py bdist_wheel && python3 setup.py install

RUN pip3 wheel scipy

RUN pip3 install ./scipy-1.4.1-cp36-cp36m-linux_aarch64.whl

RUN LANG=en_US.UTF-8 pip3 wheel scikit-image

RUN ls

FROM nvcr.io/nvidia/l4t-base:r32.4.2

ENV  DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get -y upgrade &&apt-get install -y --no-install-recommends \
    tzdata tk-dev emacs zsh git libopenblas-dev python3-matplotlib \
    libssl-dev protobuf-compiler libprotobuf-dev \
    libhdf5-serial-dev hdf5-tools libhdf5-dev zlib1g-dev zip \
    libjpeg8-dev liblapack-dev libblas-dev gfortran openmpi-bin \
    python3 python3-pip python3-dev python3-setuptools && \
    apt autoremove -y && \
    rm -rf /var/lib/apt/lists/*

ENV TZ=Asia/Tokyo

RUN pip3 install -U pip && pip3 install wheel setuptools pybind11 tqdm Cython pillow

COPY --from=0 pycuda-2019.1.2-cp36-cp36m-linux_aarch64.whl ./
COPY --from=0 torch-1.5.0-cp36-cp36m-linux_aarch64.whl ./
COPY --from=0 grpcio-1.29.0-cp36-cp36m-linux_aarch64.whl ./
COPY --from=0 onnx-1.7.0-cp36-cp36m-linux_aarch64.whl ./
COPY --from=0 cv2/dist/opencv_python-4.3.0-cp36-cp36m-linux_aarch64.whl ./
COPY --from=0 scipy-1.4.1-cp36-cp36m-linux_aarch64.whl ./
COPY --from=0 scikit_image-0.17.2-cp36-cp36m-linux_aarch64.whl ./
COPY --from=0 kiwisolver-1.2.0-cp36-cp36m-linux_aarch64.whl ./
COPY --from=0 PyWavelets-1.1.1-cp36-cp36m-linux_aarch64.whl ./

RUN pip3 install ./opencv_python-4.3.0-cp36-cp36m-linux_aarch64.whl && rm opencv_python-4.3.0-cp36-cp36m-linux_aarch64.whl && \
    pip3 install ./scipy-1.4.1-cp36-cp36m-linux_aarch64.whl && rm scipy-1.4.1-cp36-cp36m-linux_aarch64.whl && \
    pip3 install ./kiwisolver-1.2.0-cp36-cp36m-linux_aarch64.whl && rm kiwisolver-1.2.0-cp36-cp36m-linux_aarch64.whl &&\
    pip3 install ./PyWavelets-1.1.1-cp36-cp36m-linux_aarch64.whl && rm PyWavelets-1.1.1-cp36-cp36m-linux_aarch64.whl
    
RUN pip3 install ./scikit_image-0.17.2-cp36-cp36m-linux_aarch64.whl && rm scikit_image-0.17.2-cp36-cp36m-linux_aarch64.whl && \
    pip3 install git+https://github.com/albumentations-team/albumentations

RUN pip3 install ./grpcio-1.29.0-cp36-cp36m-linux_aarch64.whl && rm grpcio-1.29.0-cp36-cp36m-linux_aarch64.whl && \
    pip3 install onnx-1.7.0-cp36-cp36m-linux_aarch64.whl && rm onnx-1.7.0-cp36-cp36m-linux_aarch64.whl

RUN pip3 install ./torch-1.5.0-cp36-cp36m-linux_aarch64.whl &&\
    pip3 install --pre --extra-index-url https://developer.download.nvidia.com/compute/redist/jp/v44 "tensorflow<2" &&\
    rm ./torch-1.5.0-cp36-cp36m-linux_aarch64.whl

RUN pip3 install ./pycuda-2019.1.2-cp36-cp36m-linux_aarch64.whl && rm pycuda-2019.1.2-cp36-cp36m-linux_aarch64.whl

COPY torchvision-0.6.0a0+b68adcf-cp36-cp36m-linux_aarch64.whl /root/
RUN pip3 install /root/torchvision-0.6.0a0+b68adcf-cp36-cp36m-linux_aarch64.whl &&\
    rm /root/torchvision-0.6.0a0+b68adcf-cp36-cp36m-linux_aarch64.whl
    
# install onnxruntime
COPY onnxruntime_gpu-1.3.0-cp36-cp36m-linux_aarch64.whl /root/
RUN pip3 install /root/onnxruntime_gpu-1.3.0-cp36-cp36m-linux_aarch64.whl &&\
    rm /root/onnxruntime_gpu-1.3.0-cp36-cp36m-linux_aarch64.whl

# install onnx-tf
RUN pip3 install git+https://github.com/onnx/onnx-tensorflow

ENV TERM xterm-256color
