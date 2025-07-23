# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0

# libmaxminddb

ARG base_IMAGE_TAG
FROM $base_IMAGE_TAG

ARG CONFIGURE_ENABLE_DEBUG
ARG NPROC

WORKDIR $HOME
COPY --chown=${UID}:${GID} libmaxminddb libmaxminddb
WORKDIR $HOME/libmaxminddb
RUN ./bootstrap
RUN ./configure ${CONFIGURE_ENABLE_DEBUG} --prefix=$HOME/install \
    --enable-static
RUN nice make -j${NPROC:-3} && make install
