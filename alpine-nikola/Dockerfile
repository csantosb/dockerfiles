#  Dockerfile -- for a nikola run-time environment
#  Copyright (C) 2020  Cayetano Santos
#
#  License: GPL-3.0+

FROM        alpine

MAINTAINER  csantosb <csantosb autistici org>

ENV PIP_OPTS --no-cache-dir --disable-pip-version-check
ARG _VERSION

RUN apk update && apk upgrade

RUN apk add --no-cache                                                  \
	py3-pip                                                         \
	libffi-dev                                                      \
	python3                                                         \
	libxml2                                                         \
	libxslt                                                         \
	jpeg                                                            \
								     && \
    apk add --no-cache --virtual .build-deps                            \
	gcc                                                             \
	musl-dev                                                        \
	python3-dev                                                     \
	libxml2-dev                                                     \
	libxslt-dev                                                     \
	jpeg-dev                                                        \
								     && \
    CFLAGS="$CFLAGS -L/lib"                                             \
    pip3 install $PIP_OPTS nikola$_VERSION                              \
								     && \
    apk del .build-deps                                              && \
    find /usr/lib/python3.*                                             \
	\( -type d -a -name test -o -name tests \)                      \
	-o \( -type f -a -name '*.pyc' -o -name '*.pyo' \)              \
	-exec rm -rf '{}' +

RUN apk add --no-cache                                                  \
	libstdc++                                                       \
								     && \
    apk add --no-cache --virtual .extra-build-deps                      \
	g++                                                             \
	musl-dev                                                        \
	python3-dev                                                     \
	zeromq-dev                                                      \
								     && \
    pip3 install $PIP_OPTS 'nikola[extras]'$_VERSION                    \
								     && \
    apk del .extra-build-deps                                        && \
    find /usr/lib/python3.*                                             \
	\( -type d -a -name test -o -name tests \)                      \
	-o \( -type f -a -name '*.pyc' -o -name '*.pyo' \)              \
	-exec rm -rf '{}' +

RUN apk add ca-certificates emacs-nox git

RUN emacs -nw -q --batch --eval "(progn (require 'package)(add-to-list 'package-archives '(\"melpa\" . \"https://melpa.org/packages/\") t)(package-initialize)(package-refresh-contents)(package-install 'htmlize))"

RUN nikola plugin -i orgmode

RUN rm -rf /var/cache/apk/*