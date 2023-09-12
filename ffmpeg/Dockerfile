FROM debian:bullseye AS builder

RUN apt update -y && apt install -y curl make gcc tar gzip nasm

ENV FFMPEG_VERSION=4.2
ENV PREFIX=/install
ENV DIR=/tmp/ffmpeg

RUN mkdir ${DIR}
WORKDIR ${DIR}

## Download ffmpeg https://ffmpeg.org/
RUN curl -sLO https://ffmpeg.org/releases/ffmpeg-${FFMPEG_VERSION}.tar.gz
RUN tar -x --strip-components=1 -f ffmpeg-${FFMPEG_VERSION}.tar.gz
RUN ./configure --disable-debug --disable-doc --disable-ffplay --enable-shared --enable-gpl --extra-libs=-ldl --prefix=${PREFIX}


## Build ffmpeg https://ffmpeg.org/
RUN make clean
RUN make
RUN make install
RUN make distclean
RUN hash -r

FROM debian:bullseye

RUN apt update -y && apt install -y libc6-dbg
COPY --from=builder /install/bin/ffmpeg /
COPY --from=builder /install/lib /ffmpeg-libs
COPY ./dictionaries /dictionaries
COPY ./testsuite /testsuite
