FROM archlinux/archlinux AS zero
RUN pacman -Syyu --noconfirm && \
    pacman -S --noconfirm base-devel git && \
    pacman -Scc --noconfirm && \
    rm -rf /var/cache/pacman/pkg

FROM zero AS paru
RUN  useradd -m temp 
USER temp
RUN git clone https://aur.archlinux.org/paru-bin.git /home/temp/paru-bin
WORKDIR /home/temp/paru-bin
RUN makepkg -s
USER root
RUN pacman -Scc --noconfirm && \
    rm -rf /var/cache/pacman/pkg

FROM zero AS aur
COPY --from=paru /home/temp/paru-bin/paru-bin*pkg.tar.zst /root/paru-bin.pkg.tar.zst
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
