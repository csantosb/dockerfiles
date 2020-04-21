FROM alpine

LABEL MAINTAINER csantosb <csantosb autistici org>

RUN apk --update add doxygen graphviz && \
    rm -rf /var/cache/apk/*

CMD ["doxygen", "-v"]
