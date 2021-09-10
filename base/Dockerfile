FROM manjarolinux/base AS zero
ARG BRANCH=stable
RUN pacman-mirrors --continent && \
    pacman-mirrors --fasttrack 5 && \
    pacman-mirrors --api --set-branch ${BRANCH} && \
    pacman -Syyu --noconfirm && \
    pacman -Scc --noconfirm && \
    rm -rf /var/cache/pacman/pkg

FROM zero AS paru
RUN pacman -Syu --noconfirm git && \
    useradd -m user
USER user
RUN git clone https://aur.archlinux.org/paru-bin.git /home/user/paru-bin
WORKDIR /home/user/paru-bin
RUN makepkg -s
USER root
RUN pacman -Scc --noconfirm && \
    rm -rf /var/cache/pacman/pkg

FROM zero AS aur
COPY --from=paru /home/user/paru-bin/paru-bin*pkg.tar.zst /root/paru-bin.pkg.tar.zst
RUN pacman -U --noconfirm /root/paru-bin.pkg.tar.zst && \
    rm /root/paru-bin.pkg.tar.zst && \
    pacman -Scc --noconfirm && \
    rm -rf /var/cache/pacman/pkg

FROM aur AS base
RUN paru -Syu --noconfirm sudo zsh starship && \
    paru -Scc --noconfirm && \
    rm -rf /root/.cache/paru && \
    echo "ALL    ALL = (ALL) NOPASSWD: ALL" >> /etc/sudoers
ARG USER=user
RUN useradd -m ${USER} -s /bin/zsh
USER ${USER}
RUN echo "eval $(starship init zsh)" > /home/${USER}/.zshrc && \
    mkdir /home/${USER}/.config
COPY starship.toml /home/${USER}/.config
CMD [ "/bin/zsh" ]
