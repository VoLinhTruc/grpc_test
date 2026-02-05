# Use the specified base image
FROM ghcr.io/volinhtruc/bico_ubuntu:22.04_0.0.0

ENV HTTP_PROXY=http://host.docker.internal:3128
ENV HTTPS_PROXY=http://host.docker.internal:3128
ENV http_proxy=http://host.docker.internal:3128
ENV https_proxy=http://host.docker.internal:3128

# Install build essentials and dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    autoconf \
    libtool \
    pkg-config \
    cmake \
    git \
    curl \
    wget \
    unzip \
    ca-certificates \
    libssl-dev \
    zlib1g-dev \
    && rm -rf /var/lib/apt/lists/*

# Install gRPC and Protocol Buffers from source
WORKDIR /tmp

# Install Protocol Buffers
RUN git clone --recurse-submodules -b v27.3 --depth 1 --shallow-submodules https://github.com/protocolbuffers/protobuf.git && \
    cd protobuf && \
    cmake -S . -B build \
        -DCMAKE_BUILD_TYPE=Release \
        -Dprotobuf_BUILD_TESTS=OFF \
        -DCMAKE_INSTALL_PREFIX=/usr/local && \
    cmake --build build --parallel $(nproc) && \
    cmake --install build && \
    cd .. && rm -rf protobuf

# Install gRPC
RUN git clone --recurse-submodules -b v1.66.0 --depth 1 --shallow-submodules https://github.com/grpc/grpc.git && \
    cd grpc && \
    cmake -S . -B build \
        -DCMAKE_BUILD_TYPE=Release \
        -DgRPC_INSTALL=ON \
        -DgRPC_BUILD_TESTS=OFF \
        -DgRPC_SSL_PROVIDER=package \
        -DCMAKE_INSTALL_PREFIX=/usr/local && \
    cmake --build build --parallel $(nproc) && \
    cmake --install build && \
    cd .. && rm -rf grpc

# Update library cache
RUN ldconfig

# Set environment variables
ENV PATH="/usr/local/bin:${PATH}"
ENV LD_LIBRARY_PATH="/usr/local/lib:${LD_LIBRARY_PATH}"

# Set working directory
WORKDIR /workspaces

CMD ["/bin/bash"]
