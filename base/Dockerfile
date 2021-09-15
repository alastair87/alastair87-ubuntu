FROM ubuntu:20.04 as stage0
ENV DEBIAN_FRONTEND="noninteractive"
ARG TZ="Europe/London"
ENV TZ=${TZ}
RUN apt-get update && \
    yes | unminimize && \
    apt-get install -y tzdata && \
    rm -rf /var/cache/apt/archives


FROM stage0 AS stage-ppa
RUN apt-get update && \
    apt-get install -y --no-install-recommends software-properties-common && \
    rm -rf /var/cache/apt/archives/*
# RUN add-apt-repository -ny ppa:savoury1/backports
# RUN add-apt-repository -ny ppa:savoury1/build-tools
# RUN add-apt-repository -ny ppa:savoury1/python
# RUN add-apt-repository -ny ppa:savoury1/multimedia
RUN add-apt-repository -ny ppa:git-core/ppa
RUN add-apt-repository -ny ppa:longsleep/golang-backports
RUN add-apt-repository -ny ppa:openjdk-r/ppa
RUN add-apt-repository -ny ppa:plt/racket
RUN add-apt-repository -ny ppa:spvkgn/exa
RUN add-apt-repository -ny ppa:jonathonf/vim
RUN add-apt-repository -ny ppa:kelleyk/emacs
RUN sed -i '/deb-src/s/^# //' /etc/apt/sources.list

FROM stage0 AS stage1
COPY --from=stage-ppa /etc/apt/sources.list.d/* /etc/apt/sources.list.d/
COPY --from=stage-ppa /etc/apt/trusted.gpg.d/*  /etc/apt/trusted.gpg.d/    
RUN apt-get update && \
    apt-get dist-upgrade -y --autoremove --purge && \
    apt-get install -y zsh wget sudo && \
    rm -rf /var/cache/apt/archives/* 
RUN wget https://starship.rs/install.sh -O /tmp/install.sh && \
    chmod +x /tmp/install.sh && \
    /tmp/install.sh -y && \
    rm /tmp/install.sh
ARG USER=user
RUN useradd -m ${USER} && \
    chsh -s /bin/zsh ${USER}
RUN echo 'ALL ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers
USER ${USER}
RUN echo 'eval "$(starship init zsh)"' > /home/${USER}/.zshrc && \
    mkdir /home/${USER}/.config
COPY starship.toml /home/${USER}/.config

FROM stage1 AS stage2
USER root
RUN apt-get update 
RUN apt-get install -y man-db manpages-posix
RUN apt-get install -y build-essential
RUN apt-get install -y git
RUN apt-get install -y curl
RUN apt-get install -y exa
RUN apt-get install -y vim-nox
RUN apt-get install -y emacs-nox
RUN apt-get install -y golang-go
RUN apt-get install -y openjdk-17-jdk
RUN rm -rf /var/cache/apt/archives/*

FROM stage2 AS stage3
USER ${USER}
RUN wget https://sh.rustup.rs -O /tmp/rustup.sh && \
    sh /tmp/rustup.sh -y && \
    rm /tmp/rustup.sh
CMD [ "/bin/zsh" ]
