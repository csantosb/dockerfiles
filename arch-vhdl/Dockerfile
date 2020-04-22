#  Dockerfile -- for a vhdl (ghdl/cocotb) setup run-time environment
#  Copyright (C) 2020  Cayetano Santos
#
#  License: GPL-3.0+

FROM        archlinux

MAINTAINER  csantosb <csantosb autistici org>

RUN pacman --noconfirm -Syu

RUN pacman --noconfirm -S git ncdu make sudo diffutils fakeroot binutils

# makepkg cannot (and should not) be run as root:
RUN useradd -m notroot

# Allow notroot to run stuff as root (to install dependencies):
RUN echo "notroot ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/notroot

# Continue execution (and CMD) as notroot:
USER notroot

WORKDIR /tmp

# Auto-fetch GPG keys (for checking signatures):
# RUN mkdir .gnupg && \
#     touch .gnupg/gpg.conf && \
#     echo "keyserver-options auto-key-retrieve" > .gnupg/gpg.conf

# Install yay (for building AUR dependencies):
# RUN git clone https://aur.archlinux.org/yay-bin.git && \
#     cd yay-bin && \
#     makepkg --noconfirm --syncdeps --rmdeps --install --clean && \
#     rm -rf yay-bin

RUN git clone https://gitlab.com/aur-packages/python-cocotb-git.git && \
    cd python-cocotb-git && \
    makepkg --noconfirm --syncdeps --rmdeps --install --clean && \
    rm -rf /tmp/python-cocotb-git

RUN git clone https://aur.archlinux.org/ghdl-gcc-git.git && \
    cd ghdl-gcc-git &&\
    makepkg --noconfirm --syncdeps --rmdeps --install --clean && \
    rm -rf /tmp/ghdl-gcc-git

USER root

RUN pacman --noconfirm -Rns sudo diffutils fakeroot binutils

RUN pacman --noconfirm -Scc

RUN rm -rf /var/cache/pacman/pkg