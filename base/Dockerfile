FROM ubuntu:latest as stage0
ENV DEBIAN_FRONTEND="noninteractive"
RUN apt-get update && \
    apt-get upgrade -y && \
    rm -rf /var/cache/apt/archives/*

FROM stage0 AS stage1
RUN apt-get update && \
    apt-get install -y --no-install-recommends software-properties-common && \
    # add-apt-repository -ny ppa:jonathonf/vim && \
    # add-apt-repository -ny ppa:git-core/ppa && \
    rm -rf /var/cache/apt/archives/*

FROM stage0 AS stage2
COPY --from=stage1 /etc/apt/sources.list.d/* /etc/apt/sources.list.d/
COPY --from=stage1 /etc/apt/trusted.gpg.d/* /etc/apt/trusted.gpg.d/
RUN sed -i '/deb-src/s/^# //' /etc/apt/sources.list
RUN apt-get update
RUN yes | unminimize
RUN apt-get install -y man-db manpages-posix
RUN apt-get install -y zsh
RUN apt-get install -y git
RUN apt-get install -y build-essential
RUN rm -rf /var/cache/apt/archives/*