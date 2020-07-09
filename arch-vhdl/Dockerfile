#  Dockerfile -- for a vhdl (ghdl/cocotb) setup run-time environment
#  Copyright (C) 2020  Cayetano Santos
#
#  License: GPL-3.0+

# Contains:
#
#   cocotb:       1.4.0
#   ghdl-gcc:     current
#   osvvm:        2020.05
#   osvvm-v ip:   current
#   vivdado-libs: 2019.2

# Based on latest archlinux
FROM        archlinux

MAINTAINER  csantosb <csantosb autistici org>

# Include xilinx vivado 2019.2 /opt/Xilinx/Vivado/2019.2/data/vhdl/src dir
ADD vivado19.2_src.tar.gz /tmp

# sync repos
RUN pacman --noconfirm -Syu

# add necessary packages
RUN pacman --noconfirm -S gcc git make binutils sudo diffutils fakeroot

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

# Install python-cocotb: release 1.4.0

RUN git clone --depth=1 --branch=1.4.0 https://gitlab.com/aur-packages/python-cocotb.git && \
    cd python-cocotb && \
    makepkg --noconfirm --syncdeps --rmdeps --install --clean && \
    cd /tmp && \
    tar -czvf python-cocotb.tar.gz python-cocotb && \
    rm -rf python-cocotb-git

# Install ghdl-gcc-git: current

RUN git clone --depth=1 https://aur.archlinux.org/ghdl-gcc-git.git && \
    cd ghdl-gcc-git && \
    makepkg --noconfirm --syncdeps --rmdeps --install --clean && \
    cd /tmp && \
    tar -czvf ghdl-gcc-git.tar.gz ghdl-gcc-git && \
    rm -rf ghdl-gcc-git

USER root

# Compile xilinx vivado libraries

RUN mkdir -p /opt/Xilinx/CompiledLibraries && \
    /usr/lib/ghdl/vendors/compile-xilinx-vivado.sh --all --vhdl2008 --src /tmp/src --out /opt/Xilinx/CompiledLibraries/Vivado --ghdl /usr/bin | tee /tmp/compile_vivado.log

# Install OSVVM: release 2020.05

RUN mkdir -p /opt/HDL/osvvm/osvvm && \
    git clone --depth=1 --branch=2020.05 https://github.com/OSVVM/OSVVM.git /opt/HDL/osvvm/osvvm

# Install OSVVM VerificationIP: current

RUN mkdir -p /opt/HDL/osvvm/VerificationIP && \
    git clone --recursive --depth=1 https://github.com/OSVVM/VerificationIP.git /opt/HDL/osvvm/VerificationIP

# Compile OSVVM and its VerificationIP

RUN cd /opt/HDL && \
    git clone --depth=1 https://gitlab.com/ip-vhdl/eda-common.git && \
    cd /opt/HDL/eda-common/ghdl/vendors && \
    ./runme.sh | tee /tmp/compile_osvvm.log

# Create group ghdl, and add notroot user

RUN groupadd ghdl && \
    gpasswd -a notroot ghdl

# Xilinx libraries have write access to members of the group

RUN chown -R root:ghdl /opt/Xilinx/CompiledLibraries && \
    chmod -R g+w /opt/Xilinx/CompiledLibraries

# Osvvm libraries have write access to members of the group

RUN chown -R root:ghdl /opt/HDL/osvvm && \
    chmod -R g+w /opt/HDL/osvvm

# Cleanup things as root to reduce image size

RUN pacman --noconfirm -Rns diffutils fakeroot

RUN pacman --noconfirm -Scc

RUN rm -rf /var/cache/pacman/pkg

# Operate as non root user, yet sudo available

USER notroot

WORKDIR /home/notroot