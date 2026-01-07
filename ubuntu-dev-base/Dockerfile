FROM codercom/enterprise-base:ubuntu-20251117

USER root
ENV DEBIAN_FRONTEND=noninteractive

RUN mkdir -p /var/lib/apt/lists/partial && \
    apt-get update -y && \
    apt-get install -y --no-install-recommends \
      ca-certificates curl git unzip jq sudo gh nano zsh \
      build-essential pkg-config cmake golang-go \
      clang lld lldb gdb ninja-build ccache ffmpeg \
      openssh-client gnupg software-properties-common\
    && rm -rf /var/lib/apt/lists/*

# Install terraform
RUN curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(. /etc/os-release && echo "$VERSION_CODENAME") main" \
  | sudo tee /etc/apt/sources.list.d/hashicorp.list

RUN sudo apt-get update && \
  sudo apt-get install -y terraform

# Install zsh-autosuggestions plugin (system-wide)
RUN mkdir -p /usr/local/share/zsh/plugins && \
    git clone --depth 1 https://github.com/zsh-users/zsh-autosuggestions \
      /usr/local/share/zsh/plugins/zsh-autosuggestions

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

# Seed default zsh config for new users
RUN tee /etc/skel/.zshrc >/dev/null <<'EOF'
# Default prompt: user@host:cwd
PROMPT='%n@%m:%~%# '

# Enable zsh-autosuggestions if available
if [ -r /usr/local/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh ]; then
  source /usr/local/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
fi

# Quality-of-life options
setopt AUTO_CD            # "cd" by typing a path
setopt AUTO_PUSHD         # pushd on directory change
setopt PUSHD_IGNORE_DUPS  # no duplicate dirs in stack
setopt EXTENDED_GLOB      # enable advanced globbing
setopt NO_BEEP            # disable error beeps

# History settings
HISTSIZE=10000
SAVEHIST=10000
setopt HIST_IGNORE_DUPS
setopt SHARE_HISTORY
setopt INC_APPEND_HISTORY   # write history incrementally
setopt HIST_REDUCE_BLANKS   # trim extra blanks

# Smarter completion
autoload -Uz compinit
compinit
zstyle ':completion:*' menu select
zstyle ':completion:*' matcher-list 'm:{a-zA-Z}={A-Za-z}'
zstyle ':completion:*' squeeze-slashes true
zstyle ':completion:*' use-cache on
zstyle ':completion:*' cache-path ~/.zcompcache

# Key bindings for history search
bindkey '^R' history-incremental-search-backward
bindkey '^[[A' history-search-backward
bindkey '^[[B' history-search-forward
EOF

# Set zsh as the default shell for coder user
RUN chsh -s $(which zsh) coder

USER coder
