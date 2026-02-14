FROM ghcr.io/nicfox77/workspace-images/ubuntu-dev-base:latest

# Copy pre-compiled RocksDB from the dedicated RocksDB image
# This avoids recompiling RocksDB when the dev base changes
COPY --from=ghcr.io/nicfox77/workspace-images/ubuntu-rocksdb:latest \
  /opt/rocksdb /opt/rocksdb

USER root
ENV DEBIAN_FRONTEND=noninteractive

ARG ROCKS_VERSION=10.6.2
ENV ROCKS_VERSION=${ROCKS_VERSION}
ENV ROCKSDB_PREFIX=/opt/rocksdb/${ROCKS_VERSION}

# Install runtime dependencies for RocksDB (libraries it links against)
RUN mkdir -p /var/lib/apt/lists/partial && \
  apt-get update -y && \
  apt-get install -y --no-install-recommends \
  libsnappy1v5 liblz4-1 libzstd1 zlib1g libbz2-1.0 \
  && rm -rf /var/lib/apt/lists/*

# Optional (nice for tools), but your bms-build.sh will set its own env anyway.
ENV LD_LIBRARY_PATH="/opt/rocksdb/${ROCKS_VERSION}/lib"
ENV CGO_CFLAGS="-I/opt/rocksdb/${ROCKS_VERSION}/include"
ENV CGO_LDFLAGS="-L/opt/rocksdb/${ROCKS_VERSION}/lib -lrocksdb -lstdc++ -lm -lz -lsnappy -llz4 -lzstd -lbz2"

USER coder
