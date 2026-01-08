FROM ghcr.io/nicfox77/workspace-images/ubuntu-dev-base:latest

USER root
ENV DEBIAN_FRONTEND=noninteractive

ARG ROCKS_VERSION=10.9.1
ENV ROCKS_VERSION=${ROCKS_VERSION}
ENV ROCKSDB_PREFIX=/opt/rocksdb/${ROCKS_VERSION}

RUN mkdir -p /var/lib/apt/lists/partial && \
  apt-get update -y && \
  apt-get install -y --no-install-recommends \
  ca-certificates curl \
  build-essential pkg-config \
  libsnappy-dev liblz4-dev libzstd-dev zlib1g-dev libbz2-dev \
  libgflags-dev \
  mosquitto mosquitto-clients \
  && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /etc/mosquitto/conf.d /var/log/mosquitto /var/lib/mosquitto /opt/ps/certs && \
    chown -R mosquitto:mosquitto /var/log/mosquitto /var/lib/mosquitto

RUN tee /etc/mosquitto/conf.d/perseeva.conf >/dev/null <<'EOF'
persistence true
persistence_location /var/lib/mosquitto/

log_dest file /var/log/mosquitto/mosquitto.log

log_timestamp_format %Y-%m-%dT%H:%M:%S

max_inflight_messages 100
max_queued_messages 0

certfile /opt/ps/certs/managerCertificate.pem
keyfile /opt/ps/certs/managerKey.key
require_certificate true
tls_keyform pem

listener 8883 0.0.0.0
allow_anonymous true
protocol mqtt
EOF

RUN set -eux; \
  cd /tmp; \
  # -f makes curl FAIL on 404 instead of downloading "404: Not Found"
  curl -fsSL "https://github.com/facebook/rocksdb/archive/refs/tags/v${ROCKS_VERSION}.tar.gz" -o rocksdb.tgz; \
  tar -xzf rocksdb.tgz; \
  cd "rocksdb-${ROCKS_VERSION}"; \
  PORTABLE=haswell ROCKSDB_DISABLE_TESTS=1 make -j"$(nproc)" shared_lib static_lib; \
  install -d "${ROCKSDB_PREFIX}/include" "${ROCKSDB_PREFIX}/lib"; \
  cp -a include/rocksdb "${ROCKSDB_PREFIX}/include/"; \
  cp -a librocksdb.so* librocksdb.a "${ROCKSDB_PREFIX}/lib/"; \
  rm -rf /tmp/rocksdb* /tmp/rocksdb.tgz

# Optional (nice for tools), but your bms-build.sh will set its own env anyway.
ENV LD_LIBRARY_PATH="/opt/rocksdb/${ROCKS_VERSION}/lib"
ENV CGO_CFLAGS="-I/opt/rocksdb/${ROCKS_VERSION}/include"
ENV CGO_LDFLAGS="-L/opt/rocksdb/${ROCKS_VERSION}/lib -lrocksdb -lstdc++ -lm -lz -lsnappy -llz4 -lzstd -lbz2"

USER coder
