# htop in a container
#
# docker run --rm -it \
# 	--pid host \
# 	westonsteimel/htop
#

ARG HTOP_VERSION="2.2.0"

FROM alpine:edge as builder

ARG HTOP_VERSION

WORKDIR build

RUN apk update && apk add --no-cache \
    ncurses-dev \
    python3 \
    git \
    build-base \
    ca-certificates \
    autoconf \
    automake \
    ncurses-libs

RUN git clone --depth 1 --branch "${HTOP_VERSION}" https://github.com/hishamhm/htop \
    && cd htop \
    && ./autogen.sh \
    && ./configure \
		--build=$CBUILD \
		--host=$CHOST \
		--prefix=/usr/local \
		--sysconfdir=/etc \
		--mandir=/usr/share/man \
		--localstatedir=/var \
		--enable-cgroup \
		--enable-taskstats \
	&& make \
    && make install

RUN addgroup htop \
    && adduser -G htop -s /bin/sh -D htop

FROM alpine:edge

ARG HTOP_VERSION

RUN apk update && apk add --no-cache ncurses-libs

COPY --from=builder /etc/passwd /etc/passwd
COPY --from=builder /usr/local/bin/htop /usr/local/bin/htop

USER htop

CMD [ "/usr/local/bin/htop" ]