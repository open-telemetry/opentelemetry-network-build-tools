# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0

# libuv

ARG base_IMAGE_TAG
FROM $base_IMAGE_TAG

ARG NPROC
ARG BUILD_CFLAGS

WORKDIR $HOME
RUN sudo apt remove -y libuv1
COPY --chown=1000:1000 libuv libuv
WORKDIR $HOME/libuv
RUN ./autogen.sh
WORKDIR $HOME/build/libuv
RUN $HOME/libuv/configure --prefix=$HOME/install
RUN CFLAGS=`echo ${BUILD_CFLAGS} | sed 's/\\\\ / /g'`; nice make -j${NPROC:-3} && make install
