FROM golang:1.14.3

ADD ./bin/rr /usr/bin/rr
COPY ./rr.yaml /etc/rr.yaml
COPY ./entrypoint.sh /entrypoint.sh
ADD ./entrypoint.sh /opt/entrypoint.sh

RUN set -x \
    && apt-get update -y \
    && apt-get install -y bash git \
    && go version \
    # && bash ./build.sh \
    && test -f /usr/bin/rr \
    && test -f /etc/rr.yaml \
    && test -f /entrypoint.sh

CMD ["/bin/bash", "/opt/entrypoint.sh"]

EXPOSE 8081