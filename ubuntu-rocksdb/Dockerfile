ARG BASE_IMAGE=ghcr.io/Nicfox77/workspace-images/ubuntu-dev-base:latest
FROM ${BASE_IMAGE}
ARG ROCKS_VERSION=10.5.1

RUN apt-get update -y && apt-get install -y --no-install-recommends \
  libsnappy-dev liblz4-dev libzstd-dev zlib1g-dev libbz2-dev \
  && rm -rf /var/lib/apt/lists/*

RUN set -eux; \
  mkdir -p /tmp/build && cd /tmp/build; \
  curl -L "https://github.com/facebook/rocksdb/archive/refs/tags/v${ROCKS_VERSION}.tar.gz" -o rocksdb.tar.gz; \
  tar xzf rocksdb.tar.gz; \
  cd "rocksdb-${ROCKS_VERSION}"; \
  mkdir -p build && cd build; \
  cmake -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_INSTALL_PREFIX="/opt/rocksdb/${ROCKS_VERSION}" \
        -DWITH_TESTS=OFF -DWITH_BENCHMARK_TOOLS=OFF -DWITH_TOOLS=OFF \
        -DWITH_MD_LIBRARY=OFF -DWITH_RUNTIME_DEBUG=OFF \
        -DWITH_SNAPPY=ON -DWITH_LZ4=ON -DWITH_ZLIB=ON -DWITH_ZSTD=ON -DWITH_BZ2=ON \
        -DROCKSDB_BUILD_SHARED=ON -DPORTABLE=1 ..; \
  make -j"$(nproc)" install; \
  rm -rf /tmp/build

RUN tee /etc/profile.d/rocksdb-cgo.sh >/dev/null <<'EOF'
export ROCKSDB_VERSION="10.5.1"
export ROCKSDB_PREFIX="/opt/rocksdb/${ROCKSDB_VERSION}"
export LD_LIBRARY_PATH="$ROCKSDB_PREFIX/lib:${LD_LIBRARY_PATH:-}"
export CGO_CFLAGS="-I$ROCKSDB_PREFIX/include"
export CGO_LDFLAGS="-L$ROCKSDB_PREFIX/lib -lrocksdb -lstdc++ -lm -lz -lsnappy -llz4 -lzstd -lbz2"
EOF
