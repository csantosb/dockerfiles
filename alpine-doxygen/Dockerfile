#  Dockerfile -- for a nikola run-time environment
#  Copyright (C) 2020  Cayetano Santos
#
#  License: GPL-3.0+

FROM alpine

MAINTAINER  csantosb <csantosb autistici org>

RUN apk update && apk upgrade

RUN apk add doxygen graphviz

RUN rm -rf /var/cache/apk/*

CMD ["doxygen", "-v"]
