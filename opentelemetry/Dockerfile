# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0

# opentelemetry

ARG base_IMAGE_TAG

FROM $base_IMAGE_TAG

WORKDIR $HOME
COPY opentelemetry-proto opentelemetry-proto
WORKDIR $HOME/opentelemetry-proto

# generate c++ grpc client library

RUN PROTO_FILES=$(find ./opentelemetry -name '*.proto'); \
    for PROTO_FILE in ${PROTO_FILES}; do \
      DIR=$(dirname $PROTO_FILE); \
      mkdir -p $HOME/install/$DIR; \
      protoc -I . --cpp_out=$HOME/install ${PROTO_FILE}; \
    done

RUN PROTO_FILES=$(find ./opentelemetry -name '*service.proto'); \
    for PROTO_FILE in ${PROTO_FILES}; do \
      DIR=$(dirname $PROTO_FILE); \
      mkdir -p $HOME/install/$DIR; \
      protoc -I . --cpp_out=$HOME/install --grpc-cpp_out=$HOME/install ${PROTO_FILE}; \
    done
