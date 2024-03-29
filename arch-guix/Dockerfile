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
#   vivado-libs:      2020.2
#   fusesoc:          1.12.0

# Based on latest archlinux

FROM        archlinux

MAINTAINER  csantosb <csantosb autistici org>

# * Arch related

# ** sync repos

# TEMP-FIX for pacman issue
# https://serverfault.com/questions/1052963/pacman-doesnt-work-in-docker-image
# RUN patched_glibc=glibc-linux4-2.33-4-x86_64.pkg.tar.zst \
#     && curl -LO "https://raw.githubusercontent.com/sickcodes/Docker-OSX/master/${patched_glibc}" \
#     && bsdtar -C / -xvf "${patched_glibc}" || echo "Everything is fine."
# TEMP-FIX for pacman issue

RUN pacman --noconfirm -Syu

# ** include necessary packages

# tar and xz are necessary
# RUN pacman --noconfirm -S gcc git make binutils sudo diffutils fakeroot fusesoc
RUN pacman --noconfirm -S wget which

# ** create a non root user

# makepkg cannot (and should not) be run as root, so we need to create a non root user here

# RUN useradd -m notroot

# allow notroot to run stuff as root (to install dependencies):

# RUN echo "notroot ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/notroot

# create group ghdl, and add notroot user

# RUN groupadd ghdl && \
#     gpasswd -a notroot ghdl

# * Packages

# Continue execution as non root user to install necessary aur packages

# USER notroot

# get to a temporary dir to donwload packages to install

WORKDIR /tmp

RUN wget https://git.savannah.gnu.org/cgit/guix.git/plain/etc/guix-install.sh
RUN chmod +x guix-install.sh
RUN yes | ./guix-install.sh


# https://github.com/metacall/guix/blob/master/Dockerfile

RUN ~root/.config/guix/current/bin/guix-daemon --build-users-group=guixbuild

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

# RUN git clone --depth=1 --branch=1.5.2 https://gitlab.com/aur-packages/python-cocotb.git && \
#     cd python-cocotb && \
#     makepkg --noconfirm --syncdeps --rmdeps --install --clean && \
#     cd /tmp && \
#     tar -czvf python-cocotb.tar.gz python-cocotb && \
#     rm -rf python-cocotb && \
#     python -c 'import cocotb; print("All OK")'

# ** install ghdl-llvm-nighty

# Archlinux CXXFLAGS array in /etc/makepkg.conf includes the -Werror=format-security flag, which produces an error.

# RUN git clone --depth=1 https://gitlab.com/aur-packages/ghdl-nighty.git && \
#     cd ghdl-nighty && \
#     makepkg CXXFLAGS="$CXXFLAGS -Wno-format-security" --skippgpcheck --noconfirm --syncdeps --rmdeps --clean && \
#     sudo pacman -U ghdl-llvm-nightly-1-x86_64.pkg.tar.zst --noconfirm && \
#     cd /tmp && \
#     tar -czvf ghdl-nighty.tar.gz ghdl-nighty && \
#     rm -rf ghdl-nighty && \
#     ghdl --version

# switch to root user

# USER root

# ** xilinx vivado libraries, 2020.2

# contents of /opt/Xilinx/Vivado/2020.2/data/vhdl/src directory

# ADD vivado20.2_src.tar.gz /tmp

# compile xilinx libraries

# RUN mkdir -p /opt/Xilinx/CompiledLibraries && \
#     /usr/lib/ghdl/vendors/compile-xilinx-vivado.sh --all --vhdl2008 --source /tmp/src --output /opt/Xilinx/CompiledLibraries/Vivado --ghdl /usr/bin/ghdl | tee /tmp/compile_vivado.log

# ** download and compile OsvvmLibraries

# RUN git clone --depth=1 https://gitlab.com/ip-vhdl/eda-common.git && \
#     cd eda-common/ghdl/vendors && \
#     ./runme.sh | tee /tmp/compile_osvvm.log && \
#     rm -rf eda-common

# ** give write access to members of the group ghdl to libraries

# RUN chown -R root:ghdl /opt/Xilinx/CompiledLibraries && \
#     chmod -R g+w /opt/Xilinx/CompiledLibraries

# RUN chown -R root:ghdl /opt/HDL/osvvm && \
#     chmod -R g+w /opt/HDL/osvvm

# * Cleanup things as root to reduce image size

# RUN pacman --noconfirm -Rns diffutils fakeroot

# RUN pacman --noconfirm -Scc

# RUN rm -rf /var/cache/pacman/pkg

# * Temporally include generic.py edalize backend here by hand
#
# while this issue is resolved
#
# https://github.com/olofk/edalize/issues/181

# RUN pacman --noconfirm -S wget

# RUN wget https://raw.githubusercontent.com/olofk/edalize/generic_tool/edalize/generic.py && \
#     mv generic.py /usr/lib/python3.8/site-packages/edalize/.

# * Operate as non root user, yet sudo available

# USER notroot

# WORKDIR /home/notroot