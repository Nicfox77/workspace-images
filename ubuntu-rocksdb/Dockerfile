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
  build-essential pkg-config cmake \
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
  mkdir -p build && cd build; \
  cmake -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_INSTALL_PREFIX="${ROCKS_PREFIX}" \
  -DWITH_TESTS=OFF -DWITH_BENCHMARK_TOOLS=OFF -DWITH_TOOLS=OFF \
  -DWITH_SNAPPY=ON -DWITH_LZ4=ON -DWITH_ZLIB=ON -DWITH_ZSTD=ON -DWITH_BZ2=ON \
  -DROCKSDB_BUILD_SHARED=ON -DPORTABLE=1 ..; \
  make -j"$(nproc)" install; \
  \
  # Put libs where your script expects them: $GOPATH/pkg/rocksdb/
  if [ -d "${ROCKS_PREFIX}/lib" ]; then \
  cp -a "${ROCKS_PREFIX}/lib/"librocksdb.* "${ROCKS_PREFIX}/" || true; \
  fi; \
  \
  # quick sanity checks
  test -d "${ROCKS_PREFIX}/include/rocksdb"; \
  (test -f "${ROCKS_PREFIX}/librocksdb.so" || test -f "${ROCKS_PREFIX}/librocksdb.a"); \
  rm -rf /tmp/build

# These ENVs help tools that don't run your bms-build.sh, but bms-build.sh will override them anyway.
ENV LD_LIBRARY_PATH=/home/coder/go/pkg/rocksdb:${LD_LIBRARY_PATH}
ENV CGO_CFLAGS=-I/home/coder/go/pkg/rocksdb/include
ENV CGO_LDFLAGS=-L/home/coder/go/pkg/rocksdb -lrocksdb -lstdc++ -lm -lz -lsnappy -llz4 -lzstd -lbz2

USER coder
