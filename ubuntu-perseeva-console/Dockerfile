FROM ghcr.io/nicfox77/workspace-images/ubuntu-dev-base:latest

USER root

# Install common React tooling (Node.js is already in the base image)
RUN npm install -g create-react-app create-vite && \
  npm cache clean --force

USER coder
