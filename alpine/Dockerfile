ARG TAG=3.18.4
FROM alpine:${TAG} AS build

ARG BEAR_VERSION=3.1.3

RUN apk upgrade --no-cache \
    && apk add --no-cache \
    cmake \
    curl \
    fmt-dev \
    g++ \
    git \
    grpc-dev \
    gtest-dev \
    protobuf-dev \
    nlohmann-json \
    samurai \
    spdlog-dev \
    && rm -rf /var/cache/apk/*

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

FROM alpine:$TAG

COPY --from=build /opt/bear /opt/bear

RUN apk upgrade --no-cache \
    && apk add --no-cache \
    grpc-cpp \
    libprotobuf \
    spdlog \
    && rm -rf /var/cache/apk/*

# smoke test
RUN /opt/bear/bin/bear --help

ENTRYPOINT ["/opt/bear/bin/bear"]
