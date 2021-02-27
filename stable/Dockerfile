# htop in a container
#
# docker run --rm -it \
# 	--pid host \
# 	westonsteimel/htop
#

ARG SOURCE
ARG REVISION
ARG CREATED
ARG VERSION
ARG GIT_REF="${VERSION}"

FROM alpine:latest as builder

ARG GIT_REF

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

RUN git clone --depth 1 --branch "${GIT_REF}" https://github.com/htop-dev/htop \
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

ARG VERSION
ARG SOURCE
ARG REVISION
ARG CREATED

RUN apk update && apk add --no-cache ncurses-libs

COPY --from=builder /etc/passwd /etc/passwd
COPY --from=builder /usr/local/bin/htop /usr/local/bin/htop

USER htop

ENTRYPOINT [ "/usr/local/bin/htop" ]

LABEL org.opencontainers.image.title="htop" \
    org.opencontainers.image.description="htop in Docker" \ 
    org.opencontainers.image.source="${SOURCE}" \
    org.opencontainers.image.revision="${REVISION}" \
    org.opencontainers.image.created="${CREATED}" \
    org.opencontainers.image.version="${VERSION}"

