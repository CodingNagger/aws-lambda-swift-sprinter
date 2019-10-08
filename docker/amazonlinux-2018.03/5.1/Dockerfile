FROM amazonlinux:2018.03 as builder

RUN yum repolist all
RUN yum -y update && \
    yum -y group install "development tools" && \
    yum -y install ninja-build python libbsd gcc72 gcc72-c++ \
    libuuid-devel libicu-devel libicu-devel libstdc++-devel \
    libedit-devel libxml2-devel sqlite-devel swig python26-devel ncurses-devel \
    pkgconfig openssl-devel systemtap-sdt-devel tzdata rsync git wget

# removed clang from yum install to build swift specific clang

# #cmake
WORKDIR /tmp
RUN wget https://cmake.org/files/v3.15/cmake-3.15.0.tar.gz
RUN tar -xvf cmake-3.15.0.tar.gz
WORKDIR /tmp/cmake-3.15.0
RUN ./bootstrap
RUN make
RUN make install

# swift-llvm & swift clang
WORKDIR /tmp
RUN mkdir llvm
WORKDIR /tmp/llvm
RUN git clone https://github.com/apple/swift-llvm.git/ llvm
WORKDIR /tmp/llvm/llvm
RUN git checkout swift-5.1-branch
WORKDIR /tmp/llvm/llvm/tools
RUN git clone https://github.com/apple/swift-clang.git/ clang
WORKDIR /tmp/llvm/llvm/tools/clang
RUN git checkout swift-5.1-branch
WORKDIR /tmp/llvm/llvm/projects
RUN git clone https://github.com/apple/swift-compiler-rt.git/ compiler-rt
WORKDIR /tmp/llvm/llvm/projects/compiler-rt
RUN git checkout swift-5.1-branch
WORKDIR /tmp/llvm
RUN mkdir build
WORKDIR /tmp/llvm/build
RUN cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/opt/llvm ../llvm
RUN make -j4
RUN make install
ENV PATH /opt/llvm/bin:$PATH

# try to remove everything before this step except git installation and see if it still works.

# swift tools & stdlib
RUN mkdir /swift-source
WORKDIR /swift-source
RUN git clone https://github.com/apple/swift.git
RUN ./swift/utils/update-checkout --clone
RUN ./swift/utils/update-checkout --tag swift-5.1-RELEASE
RUN ./swift/utils/build-script --preset=buildbot_linux installable_package=$ROOT_DIR/swift-package.tar.gz install_destdir=/tmp/swift