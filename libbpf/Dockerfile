# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0

# compile our own libbpf

ARG base_IMAGE_TAG
FROM $base_IMAGE_TAG

ARG CMAKE_BUILD_TYPE
ARG RESTRICTED_NPROC

RUN apt-get install --no-install-recommends -y zip pkg-config libelf-dev zlib1g-dev libbfd-dev libcap-dev

WORKDIR $HOME
COPY libbpf libbpf
COPY bpftool bpftool

# Build libbpf first
WORKDIR $HOME/libbpf/src
RUN make -j ${RESTRICTED_NPROC:-1} DESTDIR=$HOME/install install

# Build bpftool (it will use the libbpf we just built)
WORKDIR $HOME/bpftool/src
RUN make -j ${RESTRICTED_NPROC:-1} DESTDIR=$HOME/install install