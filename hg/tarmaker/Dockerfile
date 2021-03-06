# Dockerfile for busybox+hg using buildroot
FROM            ubuntu:14.04

# Install packages
ENV             DEBIAN_FRONTEND noninteractive
RUN             apt-get -q update && apt-get -qyV install \
                    wget \
                    build-essential \
                    libncurses-dev \
                    rsync \
                    unzip \
                    libbz2-dev \
                    bc \
                    gnupg \
                    python \
                    libc6-i386
RUN             env --unset=DEBIAN_FRONTEND

# Create directories
RUN             mkdir -p /tmp/builder
WORKDIR         /tmp/builder

# Retrieve files and check authenticity
ENV             BR_VERSION 2016.02
RUN             wget -nv http://buildroot.uclibc.org/downloads/buildroot-$BR_VERSION.tar.gz &&\
                wget -nv http://buildroot.uclibc.org/downloads/buildroot-$BR_VERSION.tar.gz.sign &&\
                wget -nv http://uclibc.org/~jacmet/pubkey.gpg &&\
                gpg --import pubkey.gpg &&\
                gpg --verify buildroot-$BR_VERSION.tar.gz.sign

# Extract
RUN             tar -zxf buildroot-$BR_VERSION.tar.gz &&\
                mv buildroot-$BR_VERSION buildroot
WORKDIR         /tmp/builder/buildroot

# Add config
ADD             buildroot-config /tmp/builder/buildroot/.config

# Add mercurial package
ADD             mercurial /tmp/builder/buildroot/package/mercurial
RUN             echo 'source "package/mercurial/Config.in"' >> package/Config.in

# Change host python configuration to include bzip2
ADD             python.mk /tmp/builder/buildroot/package/python

# Reformat config and prep for make
RUN             make oldconfig

# Make
RUN             make

# Done!
RUN             cp /tmp/builder/buildroot/output/images/rootfs.tar /tmp/rootfs.tar

# TODO try to slim down the image by removing documentation etc.
RUN             mkdir /tmp/rootfs &&\
                tar -C /tmp/rootfs -pxf /tmp/rootfs.tar


WORKDIR         /tmp/rootfs
RUN             tar -pcf ../rootfs.tar .

# Done!
WORKDIR         /tmp
RUN             md5sum rootfs.tar > rootfs.tar.md5
