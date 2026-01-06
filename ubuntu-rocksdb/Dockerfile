FROM ghcr.io/nicfox77/workspace-images/ubuntu-dev-base:latest

USER root
ENV DEBIAN_FRONTEND=noninteractive

ARG ROCKS_VERSION=10.6.0
ENV GOPATH=/home/coder/go
ENV ROCKS_PREFIX=/home/coder/go/pkg/rocksdb

RUN mkdir -p /var/lib/apt/lists/partial && \
  apt-get update -y && \
  apt-get install -y --no-install-recommends \
  ca-certificates curl \
  build-essential \
  libsnappy-dev liblz4-dev libzstd-dev zlib1g-dev libbz2-dev \
  libgflags-dev \
  && rm -rf /var/lib/apt/lists/*

RUN set -eux; \
  mkdir -p /tmp/build; \
  mkdir -p "${ROCKS_PREFIX}"; \
  chown -R coder:coder /home/coder/go; \
  cd /tmp/build; \
  curl -L "https://github.com/facebook/rocksdb/archive/refs/tags/v${ROCKS_VERSION}.tar.gz" -o rocksdb.tar.gz; \
  tar xzf rocksdb.tar.gz; \
  cd "rocksdb-${ROCKS_VERSION}"; \
  \
  # Build using RocksDB Makefile (matches docs)
  # PORTABLE=haswell => good compromise baseline for 2013+ CPUs
  PORTABLE=haswell \
  ROCKSDB_DISABLE_TESTS=1 \
  make -j"$(nproc)" shared_lib static_lib; \
  \
  # Install into GOPATH layout expected by bms-build.sh
  mkdir -p "${ROCKS_PREFIX}/include"; \
  cp -a include/rocksdb "${ROCKS_PREFIX}/include/"; \
  cp -a librocksdb.so* "${ROCKS_PREFIX}/" || true; \
  cp -a librocksdb.a "${ROCKS_PREFIX}/" || true; \
  \
  # quick sanity checks
  test -d "${ROCKS_PREFIX}/include/rocksdb"; \
  (test -f "${ROCKS_PREFIX}/librocksdb.so" || test -f "${ROCKS_PREFIX}/librocksdb.a"); \
  rm -rf /tmp/build

# Helpful for tools that don't source your build script; your bms-build.sh overrides anyway.
ENV LD_LIBRARY_PATH="/home/coder/go/pkg/rocksdb"
ENV CGO_CFLAGS="-I/home/coder/go/pkg/rocksdb/include"
ENV CGO_LDFLAGS="-L/home/coder/go/pkg/rocksdb -lrocksdb -lstdc++ -lm -lz -lsnappy -llz4 -lzstd -lbz2"

USER coder
