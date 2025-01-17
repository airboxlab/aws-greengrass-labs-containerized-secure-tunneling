FROM ubuntu:latest AS builder

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get -y install build-essential g++ libssl-dev cmake git clang python3-dev

# get v1.5.14 via b6080e5bbf89fca176372aa7c839cd8e92edf7dc
# see https://github.com/awslabs/aws-iot-device-client/blob/b6080e5bbf89fca176372aa7c839cd8e92edf7dc/.github/build.sh#L106
RUN git clone https://github.com/awslabs/aws-iot-device-client.git && \
    cd aws-iot-device-client && \
    git checkout b6080e5bbf89fca176372aa7c839cd8e92edf7dc && \
    mkdir build && \
    cd build && \
    export CC=/usr/bin/clang && export CXX=/usr/bin/clang++ && \
    cmake ../ \
        -DCMAKE_BUILD_TYPE=MinSizeRel \
        -DBUILD_SDK=ON \
        -DLINK_DL=ON \
        -DEXCLUDE_JOBS=ON \
        -DEXCLUDE_DD=ON \
        -DEXCLUDE_FP=ON \
        -DEXCLUDE_SAMPLES=ON \
        -DEXCLUDE_SHADOW=ON \
        -DEXCLUDE_CONFIG_SHADOW=ON \
        -DEXCLUDE_SAMPLE_SHADOW=ON \
        -DDISABLE_MQTT=ON \
        && \
    cmake --build . --target aws-iot-device-client

FROM ubuntu:20.04

ENV DEBIAN_FRONTEND=noninteractive
ENV PYTHONUNBUFFERED=true

RUN apt-get update && apt-get install -y python3 python3-pip openssl cmake git clang python3-dev
RUN export CC=/usr/bin/clang && export CXX=/usr/bin/clang++ && python3 -m pip install awsiotsdk==1.9.2

RUN mkdir /app
WORKDIR /app/

COPY secure_tunnel_watcher.py ./
COPY --from=builder /aws-iot-device-client/build/aws-iot-device-client ./

CMD [ "python3", "/app/secure_tunnel_watcher.py"]
