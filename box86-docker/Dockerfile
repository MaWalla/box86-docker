FROM debian:bullseye-slim as builder
ENV DEBIAN_FRONTEND noninteractive

# install dependencies
RUN apt-get update && \
    apt-get install -y git build-essential cmake python3 && \
    rm -rf /var/lib/apt/lists/*

# clone box86 git repo
RUN git clone https://github.com/ptitSeb/box86 && mkdir /box86/build

# compile box86
WORKDIR /box86/build
RUN cmake .. -DARM_DYNAREC=ON -DRK3399=1 -DCMAKE_BUILD_TYPE=RelWithDebInfo
RUN make -j$(nproc)

# *********************************************************************
# container to install box86 in
FROM debian:bullseye-slim

# install mesa for hardware acceleration and make for installing
RUN apt-get update && \
    apt-get install -y make cmake libgl1-mesa-glx libglapi-mesa libgl1-mesa-dri libglx-mesa0 mesa-utils mesa-utils-extra &&\
    rm -rf /var/lib/apt/lists/*

# copy box86 build from above and install in container
COPY --from=builder /box86 /box86
WORKDIR /box86/build
RUN make install

# create a user and add it to relevant groups to access the gpu
# this user is also required to make using the X server possible
RUN groupadd -g 1000 box86 && \
    useradd -d /home/box86 -s /bin/bash -m box86 -u 1000 -g 1000 && \
    gpasswd -a box86 video && \
    groupadd -g 107 kvm && \
    gpasswd -a box86 kvm

# setup the user to be ready to use
USER box86
ENV HOME /home/box86
WORKDIR /home/box86
