FROM ghcr.io/nicfox77/workspace-images/ubuntu-dev-base:latest

USER root
ENV DEBIAN_FRONTEND=noninteractive

ARG FLUTTER_VERSION=3.38.6
ENV FLUTTER_VERSION=${FLUTTER_VERSION}
ENV FLUTTER_HOME=/opt/flutter
ENV PATH=${FLUTTER_HOME}/bin:$PATH

RUN mkdir -p /var/lib/apt/lists/partial && \
  apt-get update -y && \
  apt-get install -y --no-install-recommends \
  xz-utils unzip zip \
  && rm -rf /var/lib/apt/lists/*

# Flutter + Dart
RUN curl -fsSL "https://storage.googleapis.com/flutter_infra_release/releases/stable/linux/flutter_linux_${FLUTTER_VERSION}-stable.tar.xz" -o /tmp/flutter.tar.xz && \
  mkdir -p /opt && \
  tar -C /opt -xf /tmp/flutter.tar.xz && \
  rm /tmp/flutter.tar.xz && \
  ln -s /opt/flutter/bin/flutter /usr/local/bin/flutter && \
  chown -R coder:coder /opt/flutter

USER coder
