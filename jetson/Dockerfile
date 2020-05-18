FROM nvcr.io/nvidia/l4t-ml:r32.4.2-py3

ENV  DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y tzdata
ENV TZ=Asia/Tokyo

RUN apt-get update && apt-get install -y --no-install-recommends \
    emacs zsh git wget libopenblas-dev cmake ninja-build python3-opencv \
    gcc g++ make libssl-dev protobuf-compiler libprotobuf-dev \
    libhdf5-serial-dev hdf5-tools libhdf5-dev zlib1g-dev zip \
    libjpeg8-dev liblapack-dev libblas-dev gfortran \
    python3 python3-pip openmpi-bin python3-dev swig && \
    rm -rf /var/lib/apt/lists/*

RUN pip3 install -U pip && pip3 install setuptools pybind11 tqdm Cython pillow 

# install cmake
#RUN wget https://github.com/Kitware/CMake/releases/download/v3.17.0/cmake-3.17.0.tar.gz && \
#    tar zxvf cmake-3.17.0.tar.gz && cd cmake-3.17.0 && cmake . && make -j && make install && \
#    apt purge -y cmake && cd .. && rm -rf cmake-3.17.0

# install tensorflow
RUN pip3 uninstall -y tensorflow && pip3 install --pre --extra-index-url https://developer.download.nvidia.com/compute/redist/jp/v44 tensorflow

# install tensorflow_addons
#RUN git clone https://github.com/tensorflow/addons.git && \
#    cd addons && python3 ./configure.py && bazel build --enable_runfiles build_pip_pkg && \
#    bazel-bin/build_pip_pkg artifacts && \
#    pip3 install artifacts/tensorflow_addons-*.whl

# install onnxruntime
#RUN git clone --recursive https://github.com/microsoft/onnxruntime.git && \
#    cd onnxruntime && sed -e 's/-Wall//' cmake/CMakeLists.txt && \
#    ./build.sh --cuda_home /usr/local/cuda --cudnn_home /usr/lib/aarch64-linux-gnu --use_cuda --con#fig MinSizeRel --build_wheel --update --build  --parallel && \
#    pip3 install ./build/Linux/MinSizeRel/dist/onnxruntime_gpu-*.whl

# install onnx-tf
RUN pip3 install git+https://github.com/onnx/tensorflow-onnx
