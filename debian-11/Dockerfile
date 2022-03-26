# Debian-11 container for building a static version of the OPC UA Device Support

# Copyright (C) 2022 Ralph Lange <ralph.lange@gmx.de>

FROM debian:11

# Arguments
# SDK_TAR         tar file of the Unified Automation SDK C++ Client (without path)
#                 file must exist in the build context
# EPICS_VERSIONS  list of the base versions to build against

ARG SDK_TAR=uasdkcppclient-src-debian9.13-x86_64-gcc6.3.0-v1.7.4-520.tar.gz
ARG EPICS_VERSIONS="3.15.9 7.0.6.1"

LABEL maintainer="Ralph Lange" \
      org.opencontainers.image.authors="ralph.lange@iter.org"

LABEL name="opcua-builder-debian11" version="20220326" \
      summary="Provides the Debian 11 environment to build the OPC UA Device Support (binary) distribution." \
      io.k8s.display-name="OPC UA binary builder (Debian 11)"

ENV DEBIAN_FRONTEND=noninteractive WORKDIR=/build

RUN apt-get update \
 && apt-get -y install --no-install-recommends build-essential gcc g++ libssl-dev libxml2-dev \
    cmake wget ca-certificates \
 && rm -rf /var/lib/apt/lists/*

WORKDIR ${WORKDIR}

ADD ${SDK_TAR} .

RUN wget -O gtest-1.0.1.tar.gz https://github.com/epics-modules/gtest/archive/refs/tags/v1.0.1.tar.gz \
 && for v in ${EPICS_VERSIONS}; do \
    wget -O base-${v}.tar.gz https://epics-controls.org/download/base/base-${v}.tar.gz; \
	mkdir ${WORKDIR}/${v}; \
	done

RUN for v in ${EPICS_VERSIONS}; do \
    cd ${WORKDIR}/${v}; \
	tar xzf ../base-${v}.tar.gz; \
	mv base-${v} base; \
	make -s -C base -j all clean; \
	echo -e "GTEST=${WORKDIR}/${v}/gtest\nOPCUA=${WORKDIR}/${v}/opcua\nEPICS_BASE=${WORKDIR}/${v}/base" > RELEASE.local; \
	tar xzf ../gtest-1.0.1.tar.gz; \
	mv gtest-1.0.1 gtest; \
	make -s -C gtest -j all clean; \
	done

CMD ["/bin/bash"]
