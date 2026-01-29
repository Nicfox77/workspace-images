FROM ghcr.io/nicfox77/workspace-images/ubuntu-dev-base:latest

USER root
ENV DEBIAN_FRONTEND=noninteractive

# Install Node.js v20.x
RUN curl -fsSL https://deb.nodesource.com/setup_20.x | bash - && \
  apt-get install -y nodejs && \
  rm -rf /var/lib/apt/lists/*

# Enable corepack so pnpm/yarn are available when needed.
RUN corepack enable

# Install common React tooling
RUN npm install -g create-react-app create-vite && \
  npm cache clean --force

USER coder
