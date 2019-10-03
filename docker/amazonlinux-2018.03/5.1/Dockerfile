FROM amazonlinux:2018.03 as builder

RUN yum repolist all
RUN yum -y update && \
    yum -y group install "development tools" && \
    yum -y install ninja-build clang python \
    libuuid-devel libicu-devel libicu-devel libstdc++-devel \
    libedit-devel libxml2-devel sqlite-devel swig python26-devel ncurses-devel \
    pkgconfig openssl-devel systemtap-sdt-devel tzdata rsync git wget


#cmake
WORKDIR /tmp
RUN wget https://cmake.org/files/v3.15/cmake-3.15.0.tar.gz
RUN tar -xvf cmake-3.15.0.tar.gz
WORKDIR /tmp/cmake-3.15.0
RUN ./bootstrap
RUN make
RUN make install

RUN mkdir /swift-source
WORKDIR /swift-source
RUN git clone https://github.com/apple/swift.git
RUN ./swift/utils/update-checkout --clone
RUN ./swift/utils/update-checkout --tag swift-5.1-RELEASE
RUN ./swift/utils/build-script --preset=buildbot_linux installable_package=$ROOT_DIR/swift-package.tar.gz install_destdir=/tmp/swift