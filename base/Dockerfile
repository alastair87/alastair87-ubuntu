FROM ubuntu:20.04 AS stage0
RUN apt-get update && \
    apt-get install -y wget curl && \
    rm -rf /var/cache/apt/archives/* && \
    rm -rf /var/cache/apt/lists/*

FROM stage0 AS stage-ppa
RUN apt-get update && \
    apt-get install -y software-properties-common && \
    rm -rf /var/cache/apt/archives/* && \
    rm -rf /var/cache/apt/lists/*
RUN add-apt-repository -ny ppa:savoury1/backports
RUN add-apt-repository -ny ppa:savoury1/build-tools
RUN add-apt-repository -ny ppa:savoury1/python
RUN add-apt-repository -ny ppa:savoury1/multimedia
RUN add-apt-repository -ny ppa:savoury1/ffmpeg4
RUN add-apt-repository -ny ppa:m-grant-prg/utils
RUN add-apt-repository -ny ppa:longsleep/golang-backports
RUN add-apt-repository -ny ppa:openjdk-r/ppa
RUN add-apt-repository -ny ppa:plt/racket
RUN add-apt-repository -ny ppa:spvkgn/exa
RUN add-apt-repository -ny ppa:jonathonf/vim
RUN add-apt-repository -ny ppa:kelleyk/emacs
RUN add-apt-repository -ny ppa:hvr/ghc
RUN add-apt-repository -ny ppa:stebbins/handbrake-releases
RUN add-apt-repository -ny ppa:m-grant-prg/utils
RUN curl -fsSL https://deb.nodesource.com/gpgkey/nodesource.gpg.key | apt-key add - && \
    echo "deb https://deb.nodesource.com/node_14.x focal main\ndeb-src https://deb.nodesource.com/node_14.x focal main" > /etc/apt/sources.list.d/mkvtoolnix.nodesource.list
RUN curl -fsSl https://mkvtoolnix.download/gpg-pub-moritzbunkus.gpg | apt-key add - && \
    echo "deb https://mkvtoolnix.download/ubuntu/ focal main\ndeb-src https://mkvtoolnix.download/ubuntu/ focal main" > /etc/apt/sources.list.d/mkvtoolnix.download.list
RUN wget https://mediaarea.net/repo/deb/repo-mediaarea_1.0-19_all.deb -O /tmp/repo-mediaarea.deb && \
    dpkg -i /tmp/repo-mediaarea.deb && \
    rm /tmp/repo-mediaarea.deb
RUN sed -i '/^#\sdeb /s/^# *//' "/etc/apt/sources.list" && \
    sed -i '/^#\sdeb-src /s/^# *//' "/etc/apt/sources.list" && \
    for f in /etc/apt/sources.list.d/*; do sh -c "sed -i '/^#\sdeb /s/^# *//' $f; sed -i '/^#\sdeb-src /s/^# *//' $f"; done;  

FROM stage0 AS stage1
ENV DEBIAN_FRONTEND="noninteractive"
ARG TZ="Europe/London"
ENV TZ=${TZ}
RUN rm -rf /etc/apt/*
COPY --from=stage-ppa /etc/apt/ /etc/apt/
RUN apt-get update    
RUN apt-get dist-upgrade -y --autoremove --purge && rm -rf /var/cache/apt/archives/*
RUN yes | unminimize && rm -rf /var/cache/apt/archives/* 
RUN apt-get install -y man-db && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y manpages-posix && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y tzdata && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y sudo && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y zsh && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y git && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y build-essential && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y exa && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y vim-nox && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y emacs27-nox && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y golang-go && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y openjdk-17-jdk && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y racket  && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y racket-doc && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y texlive-base && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y markdown && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y pandoc && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y graphviz && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y graphviz-doc && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y smlnj && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y smlnj-doc  && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y haskell-platform && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y haskell-platform-doc && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y nodejs && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y handbrake-cli && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y rlwrap && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y get-iplayer && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y mediainfo && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y mkvtoolnix && rm -rf /var/cache/apt/archives/*
RUN apt-get install -y python-is-python3 python3-pip && rm -rf /var/cache/apt/archives/*
RUN rm -rf /var/cache/apt/lists/*

FROM stage1 AS stage2
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
RUN wget https://sh.rustup.rs -O /tmp/rustup.sh && \
    sh /tmp/rustup.sh -y && \
    rm /tmp/rustup.sh
CMD [ "/bin/zsh" ]