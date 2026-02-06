FROM ghcr.io/nicfox77/workspace-images/ubuntu-perseeva-backend-base:latest

USER root
ENV DEBIAN_FRONTEND=noninteractive

ARG ROCKS_VERSION=10.6.2
ENV ROCKS_VERSION=${ROCKS_VERSION}
ENV ROCKSDB_PREFIX=/opt/rocksdb/${ROCKS_VERSION}

RUN mkdir -p /var/lib/apt/lists/partial && \
  apt-get update -y && \
  apt-get install -y --no-install-recommends \
  ca-certificates curl \
  mosquitto mosquitto-clients \
  ansible \
  ruby-full \
  && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /etc/mosquitto/conf.d /var/log/mosquitto /var/lib/mosquitto /opt/ps/certs && \
    chown -R mosquitto:mosquitto /var/log/mosquitto /var/lib/mosquitto

RUN tee /etc/mosquitto/conf.d/perseeva.conf >/dev/null <<'EOF'
persistence true
persistence_location /home/coder/mosquitto/data

log_dest file /home/coder/mosquitto/log/mosquitto.log

log_timestamp_format %Y-%m-%dT%H:%M:%S

max_inflight_messages 100
max_queued_messages 0

certfile /home/coder/go/bin/certs/serverCertificate.pem
keyfile /home/coder/go/bin/certs/serverKey.key
cafile /home/coder/go/bin/certs/CACertificate.pem
require_certificate true
tls_keyform pem

listener 8883 0.0.0.0
allow_anonymous true
protocol mqtt
EOF

# Optional (nice for tools), but your bms-build.sh will set its own env anyway.
ENV LD_LIBRARY_PATH="/opt/rocksdb/${ROCKS_VERSION}/lib"
ENV CGO_CFLAGS="-I/opt/rocksdb/${ROCKS_VERSION}/include"
ENV CGO_LDFLAGS="-L/opt/rocksdb/${ROCKS_VERSION}/lib -lrocksdb -lstdc++ -lm -lz -lsnappy -llz4 -lzstd -lbz2"

USER coder
