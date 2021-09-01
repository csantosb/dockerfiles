# * Heading

#  Dockerfile -- for a nikola setup run-time environment
#  Copyright (C) 2020  Cayetano Santos
#
#  License: GPL-3.0+

# Based on latest archlinux
FROM        archlinux

MAINTAINER  csantosb <csantosb autistici org>

# * Arch related

# ** sync repos

RUN pacman --noconfirm -Syu

# ** include necessary packages

RUN pacman --noconfirm -S gcc git make binutils sudo diffutils fakeroot nikola emacs-nox

# ** create a non root user

# makepkg cannot (and should not) be run as root, so we need to create a non root user here

RUN useradd -m notroot

# Allow notroot to run stuff as root (to install dependencies):

RUN echo "notroot ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/notroot

# * Packages

# Continue execution as non root user to install necessary aur packages

# USER notroot

# get to a temporary dir to donwload packages to install

# WORKDIR /tmp

# * Cleanup things as root to reduce image size

RUN pacman --noconfirm -Rns diffutils fakeroot

RUN pacman --noconfirm -Scc

RUN rm -rf /var/cache/pacman/pkg

# * Operate as non root user, yet sudo available

USER notroot

WORKDIR /home/notroot

RUN emacs -nw -q --batch --eval "(progn (require 'package)(add-to-list 'package-archives '(\"melpa\" . \"https://melpa.org/packages/\") t)(package-initialize)(package-refresh-contents)(package-install 'htmlize))"

RUN nikola plugin -i orgmode