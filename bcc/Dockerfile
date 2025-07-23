# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0

# compile our own bcc

ARG base_IMAGE_TAG
FROM $base_IMAGE_TAG

ARG CMAKE_BUILD_TYPE
ARG RESTRICTED_NPROC

WORKDIR $HOME
COPY bcc bcc
WORKDIR $HOME/build/bcc

RUN echo $PATH

RUN cmake \
  -G Ninja \
  -DCMAKE_INSTALL_PREFIX:PATH=$HOME/install \
  -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE} \
  -DENABLE_LLVM_SHARED=OFF \
  $HOME/bcc
RUN nice ninja -j ${RESTRICTED_NPROC:-1} && ninja -j ${RESTRICTED_NPROC:-1} install
