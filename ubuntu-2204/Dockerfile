ARG TAG=22.04
FROM ubuntu:${TAG} AS build

ARG BEAR_VERSION=3.1.3

RUN apt-get --yes -qq update \
    && apt-get --yes -qq upgrade \
    && DEBIAN_FRONTEND=noninteractive apt-get --yes -qq --no-install-recommends install \
        curl ca-certificates openssl \
        ninja-build \
        git cmake python3 \
        libfmt-dev libspdlog-dev nlohmann-json3-dev \
        libgrpc++-dev libprotobuf-dev protobuf-compiler-grpc libssl-dev \
        libgmock-dev libgtest-dev \
        build-essential pkg-config \
    && apt-get --yes -qq clean \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /tmp
RUN curl -SLO https://github.com/rizsotto/Bear/archive/refs/tags/${BEAR_VERSION}.tar.gz \
    && tar xf ${BEAR_VERSION}.tar.gz \
    && cmake \
        -S Bear-${BEAR_VERSION} \
        -B Bear-${BEAR_VERSION}-build \
        -G Ninja \
        -D CMAKE_INSTALL_PREFIX=/opt/bear \
    && cmake --build Bear-${BEAR_VERSION}-build \
    && cmake --install Bear-${BEAR_VERSION}-build

FROM ubuntu:${TAG}

RUN apt-get --yes -qq update \
    && apt-get --yes -qq upgrade \
    && DEBIAN_FRONTEND=noninteractive apt-get --yes -qq --no-install-recommends install \
        libgrpc++1 \
        libprotobuf23 \
        libspdlog1 \
    && apt-get --yes -qq clean \
    && rm -rf /var/lib/apt/lists/*

COPY --from=build /opt/bear /opt/bear

# smoke test
RUN /opt/bear/bin/bear --help

ENTRYPOINT ["/opt/bear/bin/bear"]
