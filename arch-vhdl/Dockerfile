# * Heading

#  Dockerfile -- for a vhdl (ghdl/cocotb/osvvm/fusesoc) setup run-time environment
#  Copyright (C) 2020  Cayetano Santos
#
#  License: GPL-3.0+

# Contains:
#
#   cocotb:           1.5.2
#   ghdl-llvm:        nighty
#   OsvvmLibraries:   current
#   vivdado-libs:     2019.2
#   fusesoc:          1.12.0

# Based on latest archlinux

FROM        archlinux

MAINTAINER  csantosb <csantosb autistici org>

# * Arch related

# ** sync repos

RUN pacman --noconfirm -Syu

# ** include necessary packages

RUN pacman --noconfirm -S gcc git make binutils sudo diffutils fakeroot fusesoc

# ** create a non root user

# makepkg cannot (and should not) be run as root, so we need to create a non root user here

RUN useradd -m notroot

# allow notroot to run stuff as root (to install dependencies):

RUN echo "notroot ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/notroot

# create group ghdl, and add notroot user

RUN groupadd ghdl && \
    gpasswd -a notroot ghdl

# * Packages

# Continue execution as non root user to install necessary aur packages

USER notroot

# get to a temporary dir to donwload packages to install

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

# ** install python-cocotb: release 1.5.2

RUN git clone --depth=1 --branch=1.5.2 https://gitlab.com/aur-packages/python-cocotb.git && \
    cd python-cocotb && \
    makepkg --noconfirm --syncdeps --rmdeps --install --clean && \
    cd /tmp && \
    tar -czvf python-cocotb.tar.gz python-cocotb && \
    rm -rf python-cocotb && \
    python -c 'import cocotb; print("All OK")'

# ** install ghdl-llvm-nighty

# Archlinux CXXFLAGS array in /etc/makepkg.conf includes the -Werror=format-security flag, which produces an error.

RUN git clone --depth=1 https://gitlab.com/aur-packages/ghdl-nighty.git && \
    cd ghdl-nighty && \
    makepkg CXXFLAGS="$CXXFLAGS -Wno-format-security" --skippgpcheck --noconfirm --syncdeps --rmdeps --clean && \
    sudo pacman -U ghdl-llvm-nightly-1-x86_64.pkg.tar.zst --noconfirm && \
    cd /tmp && \
    tar -czvf ghdl-nighty.tar.gz ghdl-nighty && \
    rm -rf ghdl-nighty && \
    ghdl --version

# switch to root user

USER root

# ** xilinx vivado libraries, 2019.2

# contents of /opt/Xilinx/Vivado/2019.2/data/vhdl/src directory

ADD vivado19.2_src.tar.gz /tmp

# compile xilinx libraries

RUN mkdir -p /opt/Xilinx/CompiledLibraries && \
    /usr/lib/ghdl/vendors/compile-xilinx-vivado.sh --all --vhdl2008 --source /tmp/src --output /opt/Xilinx/CompiledLibraries/Vivado --ghdl /usr/bin/ghdl | tee /tmp/compile_vivado.log

# ** download and compile OsvvmLibraries

RUN git clone --depth=1 https://gitlab.com/ip-vhdl/eda-common.git && \
    cd eda-common/ghdl/vendors && \
    ./runme.sh | tee /tmp/compile_osvvm.log && \
    rm -rf eda-common

# ** give write access to members of the group ghdl to libraries

RUN chown -R root:ghdl /opt/Xilinx/CompiledLibraries && \
    chmod -R g+w /opt/Xilinx/CompiledLibraries

RUN chown -R root:ghdl /opt/HDL/osvvm && \
    chmod -R g+w /opt/HDL/osvvm

# * Cleanup things as root to reduce image size

RUN pacman --noconfirm -Rns diffutils fakeroot

RUN pacman --noconfirm -Scc

RUN rm -rf /var/cache/pacman/pkg

# * Temporally include generic.py edalize backend here by hand
#
# while this issue is resolved
#
# https://github.com/olofk/edalize/issues/181

# RUN pacman --noconfirm -S wget

# RUN wget https://raw.githubusercontent.com/olofk/edalize/generic_tool/edalize/generic.py && \
#     mv generic.py /usr/lib/python3.8/site-packages/edalize/.

# * Operate as non root user, yet sudo available

USER notroot

WORKDIR /home/notroot