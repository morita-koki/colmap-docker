

ARG UBUNTU_VERSION=22.04
ARG NVIDIA_CUDA_VERSION=12.5.1
ARG CUDA_ARCHITECTURES=86

#
# Docker builder stage.
#
FROM nvidia/cuda:${NVIDIA_CUDA_VERSION}-devel-ubuntu${UBUNTU_VERSION}
WORKDIR /ceres
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update
RUN apt-get install -y cmake ninja-build git wget \
    libgoogle-glog-dev libgflags-dev \ 
    libatlas-base-dev \
    libeigen3-dev \
    libsuitesparse-dev 

RUN wget https://developer.download.nvidia.com/compute/cudss/0.3.0/local_installers/cudss-local-repo-ubuntu2204-0.3.0_0.3.0-1_amd64.deb
RUN dpkg -i cudss-local-repo-ubuntu2204-0.3.0_0.3.0-1_amd64.deb
RUN cp /var/cudss-local-repo-ubuntu2204-0.3.0/cudss-*-keyring.gpg /usr/share/keyrings/ &&   apt-get update &&  apt-get -y install cudss
RUN ln -s /usr/lib/x86_64-linux-gnu/libcudss/12/libcudss.so.0.3.0 /usr/lib/x86_64-linux-gnu/libcudss.so.0.3.0 && \
ln -s /usr/lib/x86_64-linux-gnu/libcudss/12/libcudss_commlayer_nccl.so.0.3.0 /usr/lib/x86_64-linux-gnu/libcudss_commlayer_nccl.so.0.3.0 && \
ln -s /usr/lib/x86_64-linux-gnu/libcudss/12/libcudss_commlayer_openmpi.so.0.3.0 /usr/lib/x86_64-linux-gnu/libcudss_commlayer_openmpi.so.0.3.0 

RUN git clone --recurse-submodules https://github.com/ceres-solver/ceres-solver 
RUN sed -i 's/\${cudss_VERSION}//g' ceres-solver/CMakeLists.txt && cd ceres-solver
# RUN mkdir build && cd build 
WORKDIR /ceres/ceres-solver/build
RUN cmake .. -DUSE_CUDA=1 -DBUILD_TESTING=OFF -DBUILD_EXAMPLES=ON -DCMAKE_CUDA_ARCHITECTURES=${CUDA_ARCHITECTURES} -GNinja
RUN ninja && ninja install


# Build and install COLMAP.
RUN apt-get update && \
    apt-get install -y --no-install-recommends --no-install-suggests \
        git \
        cmake \
        ninja-build \
        build-essential \
        libboost-program-options-dev \
        libboost-graph-dev \
        libboost-system-dev \
        libeigen3-dev \
        libflann-dev \
        libfreeimage-dev \
        libmetis-dev \
        libgoogle-glog-dev \
        libgtest-dev \
        libgmock-dev \
        libsqlite3-dev \
        libglew-dev \
        qtbase5-dev \
        libqt5opengl5-dev \
        libcgal-dev

WORKDIR /colmap
RUN git clone  --recurse-submodules https://github.com/colmap/colmap.git && cd colmap && \
    git fetch https://github.com/colmap/colmap.git ${COLMAP_GIT_COMMIT} && \
    git checkout FETCH_HEAD && \
    mkdir build && \
    cd build && \
    cmake .. -DCMAKE_CUDA_ARCHITECTURES=${CUDA_ARCHITECTURES} \
        -DCMAKE_INSTALL_PREFIX=/colmap-install -GNinja && \
    ninja && ninja install

ENV PATH=/colmap-install/bin:${PATH}
