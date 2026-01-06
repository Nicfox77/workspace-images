FROM codercom/enterprise-base:ubuntu-20251117

USER root
ENV DEBIAN_FRONTEND=noninteractive

RUN mkdir -p /var/lib/apt/lists/partial && \
    apt-get update -y && \
    apt-get install -y --no-install-recommends \
      ca-certificates curl git unzip jq sudo gh nano zsh \
      build-essential pkg-config cmake golang-go \
      clang lld lldb gdb ninja-build ccache \
      openssh-client \
    && rm -rf /var/lib/apt/lists/*

# Install zsh-autosuggestions plugin
RUN git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.zsh/custom}/plugins/zsh-autosuggestions

# Make Go tooling predictable for both startup scripts and interactive shells
ENV GOPATH=/home/coder/go
ENV GOTOOLCHAIN=auto
ENV PATH=/home/coder/go/bin:$PATH

# Create GOPATH dirs with correct ownership (optional but nice)
RUN mkdir -p /home/coder/go/{bin,pkg,src} && chown -R coder:coder /home/coder/go

# Optional: for interactive shells (doesn't hurt)
RUN tee /etc/profile.d/go.sh >/dev/null <<'EOF'
export GOPATH="/home/coder/go"
export GOTOOLCHAIN=auto
export PATH="$GOPATH/bin:$PATH"
EOF

# Configure zsh with autosuggestions
RUN tee /home/coder/.zshrc >/dev/null <<'EOF'
# Enable zsh-autosuggestions
source ~/.zsh/custom/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh

# Basic zsh configuration
export EDITOR=nano
export PAGER=less

# History settings
HISTSIZE=10000
SAVEHIST=10000
setopt HIST_IGNORE_DUPS
setopt SHARE_HISTORY

# Basic completion
autoload -Uz compinit
compinit
EOF

# Set zsh as the default shell for coder user
RUN chsh -s $(which zsh) coder

USER coder
