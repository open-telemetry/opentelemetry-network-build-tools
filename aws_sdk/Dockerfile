# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0

# AWS SDK

ARG base_IMAGE_TAG
ARG CMAKE_BUILD_TYPE

FROM $base_IMAGE_TAG

ARG NPROC

WORKDIR $HOME

COPY aws-sdk-cpp aws-sdk-cpp

WORKDIR $HOME/build/aws-sdk-cpp

RUN cmake \
  -DCUSTOM_MEMORY_MANAGEMENT=0 \
  -DBUILD_SHARED_LIBS=OFF \
  -DBUILD_ONLY="ec2;s3" \
  -DFORCE_CURL=ON \
  -DUSE_OPENSSL=ON \
  -DENABLE_TESTING=OFF \
  -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE} \
  -DCMAKE_INSTALL_PREFIX:PATH=$HOME/install \
  $HOME/aws-sdk-cpp

RUN nice make -j${NPROC:-3}
RUN nice make install
