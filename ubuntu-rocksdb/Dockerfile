# RocksDB Builder Image
# This image is dedicated to compiling RocksDB and is rarely rebuilt.
# Other images can COPY the compiled artifacts from this image.

FROM ubuntu:22.04

USER root
ENV DEBIAN_FRONTEND=noninteractive

ARG ROCKS_VERSION=10.6.2
ENV ROCKS_VERSION=${ROCKS_VERSION}
ENV ROCKSDB_PREFIX=/opt/rocksdb/${ROCKS_VERSION}
ENV CCACHE_DIR=/root/.ccache

RUN mkdir -p /var/lib/apt/lists/partial && \
  apt-get update -y && \
  apt-get install -y --no-install-recommends \
  ca-certificates curl \
  build-essential pkg-config ccache \
  libsnappy-dev liblz4-dev libzstd-dev zlib1g-dev libbz2-dev \
  libgflags-dev \
  && rm -rf /var/lib/apt/lists/*

RUN --mount=type=cache,target=/root/.ccache \
  --mount=type=cache,target=/tmp/rocksdb-cache \
  set -eux; \
  cd /tmp/rocksdb-cache; \
  # -f makes curl FAIL on 404 instead of downloading "404: Not Found"
  if [ ! -f "rocksdb-${ROCKS_VERSION}.tgz" ]; then \
    curl -fsSL "https://github.com/facebook/rocksdb/archive/refs/tags/v${ROCKS_VERSION}.tar.gz" -o "rocksdb-${ROCKS_VERSION}.tgz"; \
  fi; \
  if [ ! -d "rocksdb-${ROCKS_VERSION}" ]; then \
    tar -xzf "rocksdb-${ROCKS_VERSION}.tgz"; \
  fi; \
  cd "rocksdb-${ROCKS_VERSION}"; \
  CC="ccache gcc" CXX="ccache g++" PORTABLE=haswell ROCKSDB_DISABLE_TESTS=1 make -j"$(nproc)" shared_lib static_lib; \
  install -d "${ROCKSDB_PREFIX}/include" "${ROCKSDB_PREFIX}/lib"; \
  cp -a include/rocksdb "${ROCKSDB_PREFIX}/include/"; \
  cp -a librocksdb.so* librocksdb.a "${ROCKSDB_PREFIX}/lib/"

# Set the prefix as a label for easy discovery
LABEL rocksdb.version="${ROCKS_VERSION}"
LABEL rocksdb.prefix="${ROCKSDB_PREFIX}"
