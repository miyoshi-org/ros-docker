FROM ubuntu:20.04

ENV DEBIAN_FRONTEND=noninteractive
ENV DISABLE_ROS1_EOL_WARNINGS=1

# タイムゾーン設定
RUN ln -fs /usr/share/zoneinfo/Asia/Tokyo /etc/localtime && \
    echo "Asia/Tokyo" > /etc/timezone

# 必要なパッケージインストール
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl gnupg2 lsb-release sudo tzdata \
    zsh nano git x11-apps && \
    rm -rf /var/lib/apt/lists/*

# ROSリポジトリ追加
RUN curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | apt-key add - && \
    echo "deb http://packages.ros.org/ros/ubuntu focal main" > /etc/apt/sources.list.d/ros1.list

# ROSインストール
RUN apt-get update && apt-get install -y --no-install-recommends \
    ros-noetic-desktop \
    ros-noetic-gazebo-ros-pkgs ros-noetic-gazebo-ros-control && \
    rm -rf /var/lib/apt/lists/*

# dialoutグループ作成（既にあれば無視）
RUN groupadd -g 20 dialout || true

# ユーザー作成
RUN useradd -m -u 1000 -G dialout,sudo miyoshilab && \
    echo 'miyoshilab:miyoshilab' | chpasswd && \
    echo "miyoshilab ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# デフォルトシェルを zsh に変更
RUN chsh -s /usr/bin/zsh miyoshilab

RUN cat <<'EOF' > /home/miyoshilab/.zshrc
autoload -Uz compinit && compinit
autoload -U colors && colors

PS1="%{$fg[green]%}%n%{$reset_color%}@%{$fg[cyan]%}%m %{$fg[yellow]%}%~ %{$reset_color%}%% "

HISTFILE=~/.zsh_history
HISTSIZE=1000000
SAVEHIST=1000000
setopt INC_APPEND_HISTORY
setopt SHARE_HISTORY
setopt HIST_IGNORE_ALL_DUPS

zstyle ':completion:*' menu select

setopt NO_BEEP
setopt AUTO_CD
setopt EXTENDED_GLOB

alias ll="ls -lAh --color=auto"
eval "$(dircolors -b)"
zstyle ":completion:*" list-colors ${(s.:.)LS_COLORS}
alias rm="rm -i"
alias cp="cp -i"
alias mv="mv -i"

source /opt/ros/noetic/setup.zsh
EOF

RUN chown miyoshilab:miyoshilab /home/miyoshilab/.zshrc

# USER と WORKDIR
USER miyoshilab
WORKDIR /home/miyoshilab

CMD ["zsh"]
