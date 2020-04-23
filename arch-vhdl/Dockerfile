#  Dockerfile -- for a vhdl (ghdl/cocotb) setup run-time environment
#  Copyright (C) 2020  Cayetano Santos
#
#  License: GPL-3.0+

# Based on latest archlinux
FROM        archlinux

MAINTAINER  csantosb <csantosb autistici org>

# Include xilinx vivado 2019.2 /opt/Xilinx/Vivado/2019.2/data/vhdl/src dir
ADD vivado19.2_src.tar.gz /tmp

# sync repos
RUN pacman --noconfirm -Syu

# add necessary packages
RUN pacman --noconfirm -S doxygen graphviz gcc git make binutils sudo diffutils fakeroot

# makepkg cannot (and should not) be run as root, so we need to create a non root user here
RUN useradd -m notroot

# Allow notroot to run stuff as root (to install dependencies):
RUN echo "notroot ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/notroot

# Continue execution as non root user to install necessary aur packages
USER notroot

# Get to a temporary dir
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

# Install latest python-cocotb-git

RUN git clone https://gitlab.com/aur-packages/python-cocotb-git.git && \
    cd python-cocotb-git && \
    makepkg --noconfirm --syncdeps --rmdeps --install --clean && \
    rm -rf /tmp/python-cocotb-git

# Install latest ghdl-gcc-git. Here I need to force install with pacman as the
# --install flags makes the makepkg line break for some reason

RUN git clone https://aur.archlinux.org/ghdl-gcc-git.git && \
    cd ghdl-gcc-git && \
    makepkg --noconfirm --syncdeps --rmdeps --install --clean && \
    sudo pacman --noconfirm -U ghdl-gcc-git-*.tar.xz  && \
    rm -rf /tmp/ghdl-gcc-git

# Compile xilinx vivado libraries

RUN /usr/lib/ghdl/vendors/compile-xilinx-vivado.sh --all --vhdl2008 --src /tmp/src --out /home/notroot/CompiledLibraries/Vivado --ghdl /usr/bin

# Cleanup things as root to reduce image size

USER root

RUN pacman --noconfirm -Rns diffutils fakeroot

RUN pacman --noconfirm -Scc

RUN rm -rf /var/cache/pacman/pkg

# Operate as non root user, yet sudo available

USER notroot

WORKDIR /home/nonroot