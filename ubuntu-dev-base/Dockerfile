FROM codercom/enterprise-base:ubuntu-20251117
ARG DEBIAN_FRONTEND=noninteractive
ARG GO_VERSION=1.24.11

RUN apt-get update -y && apt-get install -y --no-install-recommends \
  ca-certificates curl git unzip jq bash sudo \
  build-essential pkg-config tar cmake \
  clang lld lldb gdb ninja-build ccache \
  openssh-client \
  docker.io \
  && rm -rf /var/lib/apt/lists/*

RUN set -eux; \
  curl -L "https://go.dev/dl/go${GO_VERSION}.linux-amd64.tar.gz" -o /tmp/go.tgz; \
  rm -rf /usr/local/go; \
  tar -C /usr/local -xzf /tmp/go.tgz; \
  rm -f /tmp/go.tgz

RUN tee /etc/profile.d/go.sh >/dev/null <<'EOF'
export GOPATH="/home/coder/go"
export PATH="/usr/local/go/bin:$GOPATH/bin:$PATH"
export GOTOOLCHAIN=auto
EOF
