FROM debian:bullseye

RUN apt update -y && apt install -y imagemagick
RUN mkdir -p /mayhem/patched-libs
COPY ./libs_patched2/libpng16.so.16.patched2 /mayhem/patched-libs/libpng16.so.16
COPY ./dictionaries /dictionaries
