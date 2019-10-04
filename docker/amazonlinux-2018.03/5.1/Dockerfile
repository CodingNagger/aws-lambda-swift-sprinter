FROM amazonlinux:2018.03 as builder

RUN yum repolist all
RUN yum -y update && \
    yum -y group install "development tools" && \
    yum -y install ninja-build python \
    libuuid-devel libicu-devel libicu-devel libstdc++-devel \
    libedit-devel libxml2-devel sqlite-devel swig python26-devel ncurses-devel \
    pkgconfig openssl-devel systemtap-sdt-devel tzdata rsync git wget

# removed clang from yum install to build swift specific clang

#cmake
WORKDIR /tmp
RUN wget https://cmake.org/files/v3.15/cmake-3.15.0.tar.gz
RUN tar -xvf cmake-3.15.0.tar.gz
WORKDIR /tmp/cmake-3.15.0
RUN ./bootstrap
RUN make
RUN make install

# swift-llvm & swift clang
WORKDIR /usr/share
RUN git clone https://github.com/apple/swift-llvm llvm-source
RUN git clone https://github.com/apple/swift-clang clang


RUN mkdir /usr/share/llvm
WORKDIR /usr/share/llvm
RUN ls -als
RUN cmake -DLLVM_ENABLE_PROJECTS=clang -G "Unix Makefiles" ../llvm-source
# RUN make 
RUN gmake
ENV PATH="/usr/share/llvm:${PATH}"
RUN ls -als

# swift tools & stdlib
RUN mkdir /swift-source
WORKDIR /swift-source
RUN git clone https://github.com/apple/swift.git
RUN ./swift/utils/update-checkout --clone
RUN ./swift/utils/update-checkout --tag swift-5.1-RELEASE
RUN ./swift/utils/build-script --preset=buildbot_incremental,tools=RA,stdlib=RA,build installable_package=$ROOT_DIR/swift-package.tar.gz install_destdir=/tmp/swift