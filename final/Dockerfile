# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0

ARG base_IMAGE_TAG
ARG bcc_IMAGE_TAG
ARG libuv_IMAGE_TAG
ARG aws_sdk_IMAGE_TAG
ARG cpp_misc_IMAGE_TAG
ARG go_IMAGE_TAG
ARG libmaxminddb_IMAGE_TAG
ARG gcp_cpp_IMAGE_TAG
ARG opentelemetry_IMAGE_TAG
ARG libbpf_IMAGE_TAG
#gen:dep-arg

FROM $base_IMAGE_TAG as build-main
FROM $bcc_IMAGE_TAG as build-bcc
FROM $libuv_IMAGE_TAG as build-libuv
FROM $aws_sdk_IMAGE_TAG as build-aws-sdk
FROM $cpp_misc_IMAGE_TAG as build-cpp-misc
FROM $go_IMAGE_TAG as build-go
FROM $libmaxminddb_IMAGE_TAG as build-libmaxminddb
FROM $gcp_cpp_IMAGE_TAG as build-gcp_cpp
FROM $opentelemetry_IMAGE_TAG as build-opentelemetry
FROM $libbpf_IMAGE_TAG as build-libbpf
#gen:dep-from

# Bring everything together
FROM build-main AS build-result

# docker packages
ARG PKG_DOCKER="docker.io docker-compose"
RUN apt-get -y update && apt-get install --no-install-recommends -y $PKG_DOCKER

# kernel tools packages
ARG PKG_KERNEL_TOOLS="kmod selinux-utils"
RUN apt-get -y update && apt-get install --no-install-recommends -y $PKG_KERNEL_TOOLS

# core tools packages
ARG PKG_CORE_TOOLS="pass"
RUN apt-get -y update && apt-get install --no-install-recommends -y $PKG_CORE_TOOLS

# dev tools packages
ARG PKG_DEV_TOOLS="vim-nox lsof"
RUN apt-get -y update && apt-get install --no-install-recommends -y $PKG_DEV_TOOLS

# aws tools packages
ARG PKG_AWS_TOOLS="awscli"
RUN apt-get -y update && apt-get install --no-install-recommends -y $PKG_AWS_TOOLS

ARG PKG_PYTHON_LIBS="ijson docker"
RUN pip3 install $PKG_PYTHON_LIBS

# set up apt repositories for google-cloud-sdk
RUN echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] http://packages.cloud.google.com/apt cloud-sdk main" | tee -a /etc/apt/sources.list.d/google-cloud-sdk.list
RUN curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key --keyring /usr/share/keyrings/cloud.google.gpg add -

ARG BENV_JAVA_VERSION=17
ARG PKG_EXTRA_PACKAGES="openjdk-${BENV_JAVA_VERSION}-jdk-headless google-cloud-sdk google-cloud-sdk-skaffold"
RUN apt-get -y update && apt-get install -y --no-install-recommends $PKG_EXTRA_PACKAGES

## java version required by render framework parser
RUN case $(uname -m) in \
      x86_64) update-alternatives --set java /usr/lib/jvm/java-${BENV_JAVA_VERSION}-openjdk-amd64/bin/java && \
              update-alternatives --set javac /usr/lib/jvm/java-${BENV_JAVA_VERSION}-openjdk-amd64/bin/javac \
              ;; \
      aarch64) update-alternatives --set java /usr/lib/jvm/java-${BENV_JAVA_VERSION}-openjdk-arm64/bin/java  && \
              update-alternatives --set javac /usr/lib/jvm/java-${BENV_JAVA_VERSION}-openjdk-arm64/bin/javac \
              ;; \
    esac


# gradle
RUN wget https://services.gradle.org/distributions/gradle-7.3.3-bin.zip -O /usr/local/lib/gradle.zip

# swagger-codegen-cli, used to build the API docs
RUN wget \
  https://repo1.maven.org/maven2/io/swagger/swagger-codegen-cli/2.4.12/swagger-codegen-cli-2.4.12.jar \
  -O /usr/local/lib/swagger-codegen-cli.jar

# copy artifacts from individual builds
COPY --from=build-bcc $HOME/install $HOME/install
COPY --from=build-libuv $HOME/install $HOME/install
COPY --from=build-aws-sdk $HOME/install $HOME/install
COPY --from=build-cpp-misc $HOME/install $HOME/install
COPY --from=build-libmaxminddb $HOME/install $HOME/install
COPY --from=build-gcp_cpp $HOME/install $HOME/install
COPY --from=build-opentelemetry $HOME/install $HOME/install
COPY --from=build-libbpf $HOME/install $HOME/install
#gen:dep-copy

COPY --from=build-go $HOME/go/bin /usr/local/go/bin
COPY --from=build-go $HOME/go/src /usr/local/go/src
COPY --from=build-go $HOME/go/pkg /usr/local/go/pkg

# Preprocessor for BPF used by cmake
RUN pip3 install pcpp

# add a script to setup build inside of container
# to be run after we build the image.
RUN ln -s $HOME/src/dev/benv-build.sh build.sh

# Licensing information
#
COPY LICENSE.txt $HOME/
COPY NOTICE.txt $HOME/

ARG BENV_UNMINIMIZE=false
RUN (which unminimize && $BENV_UNMINIMIZE && (yes | unminimize)) || true

RUN echo 'if [ -e "$HOME/src/dev/build-env/profile" ]; then source "$HOME/src/dev/build-env/profile"; fi' >> $HOME/.profile
