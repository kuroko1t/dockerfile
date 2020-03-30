FROM nvcr.io/nvidia/l4t-base:r32.3.1

ENV  DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y tzdata
ENV TZ=Asia/Tokyo

RUN apt-get update && apt-get install -y --no-install-recommends \
    emacs zsh git wget libopenblas-dev cmake ninja-build python3-opencv \
    gcc g++ make libssl-dev protobuf-compiler libprotobuf-dev \
    python3 python3-pip openmpi-bin && \
    rm -rf /var/lib/apt/lists/*

RUN pip3 install -U pip && pip3 install Cython numpy setuptools onnx
RUN wget https://nvidia.box.com/shared/static/ncgzus5o23uck9i5oth2n8n06k340l6k.whl -O torch-1.4.0-cp36-cp36m-linux_aarch64.whl && \
    pip3 install torch-1.4.0-cp36-cp36m-linux_aarch64.whl && rm torch-1.4.0-cp36-cp36m-linux_aarch64.whl

# install cmake
RUN wget https://github.com/Kitware/CMake/releases/download/v3.17.0/cmake-3.17.0.tar.gz && \
    tar zxvf cmake-3.17.0.tar.gz && cd cmake-3.17.0 && cmake . && make -j && make install && \
    apt purge -y cmake && cd .. && rm -rf cmake-3.17.0

# install bazel
RUN wget https://github.com/bazelbuild/bazel/releases/download/2.0.0/bazel-2.0.0-dist.zip && \
    mkdir bazel && mv bazel-2.0.0-dist.zip bazel && cd bazel && ./compile.sh && \
    mkdir $HOME/bin && mv output/bazel $HOME/bin && cd && rm -rf bazel

ENV PATH=$PATH:$HOME/bin/

# install tensorflow
RUN sudo  && sudo pip3 install --pre --extra-index-url https://developer.download.nvidia.com/compute/redist/jp/v43 tensorflow-gpu

# install tensorflow_addons
RUN git clone https://github.com/tensorflow/addons.git && \
    cd addons && python3 ./configure.py && bazel build --enable_runfiles build_pip_pkg && \
    bazel-bin/build_pip_pkg artifacts && \
    pip3 install artifacts/tensorflow_addons-*.whl

# install onnx-tf
RUN pip3 install git+https://github.com/onnx/onnx-tensorflow