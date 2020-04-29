#  Dockerfile -- for a testing setup run-time environment
#  Copyright (C) 2020  Cayetano Santos
#
#  License: GPL-3.0+

# Based on latest archlinux
FROM        archlinux

MAINTAINER  csantosb <csantosb autistici org>

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

# Install yay (for building AUR dependencies):
RUN git clone https://aur.archlinux.org/yay-bin.git && \
    cd yay-bin && \
    makepkg --noconfirm --syncdeps --rmdeps --install --clean && \
    rm -rf yay-bin

# Cleanup things as root to reduce image size

USER root

RUN pacman --noconfirm -Scc

RUN rm -rf /var/cache/pacman/pkg

# Operate as non root user, yet sudo available

USER notroot

WORKDIR /home/notroot