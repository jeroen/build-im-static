
FROM ubuntu:22.04

WORKDIR /root

# ------------------------------------------------------------------------------------
# clang 20, gfortran-12

RUN export DEBIAN_FRONTEND=noninteractive && \
    apt-get update -y && \
    apt-get install -y gnupg curl && \
    echo "deb http://apt.llvm.org/jammy/ llvm-toolchain-jammy-20 main" \
         > /etc/apt/sources.list.d/llvm.list && \
    echo "deb-src http://apt.llvm.org/jammy/ llvm-toolchain-jammy-20 main" \
         >> /etc/apt/sources.list.d/llvm.list && \
    curl -L https://apt.llvm.org/llvm-snapshot.gpg.key | apt-key add - && \
    apt-get update -y && \
    apt-get install -y clang-20 libc++abi-20-dev libc++-20-dev && \
    apt-get clean

RUN ln -s /usr/bin/clang-20 /usr/bin/clang && \
    ln -s /usr/bin/clang++-20 /usr/bin/clang++

# dependencies
RUN apt-get install -y make pkg-config libjpeg8-dev libpng-dev libtiff-dev libxml2-dev

# Mirror: https://github.com/ropensci/magick/releases/download/ImageMagick-6.9.13-25/ImageMagick-6.9.13-25.tar.gz
RUN curl -LO https://www.imagemagick.org/archive/ImageMagick-6.9.13-25.tar.gz && \
    tar xf ImageMagick-6.9.13-25.tar.gz && \
    rm ImageMagick-6.9.13-25.tar.gz

RUN cd ImageMagick-6.9.13-25 && \
    CC="clang -fPIC" CXX="clang++ -stdlib=libc++ -fPIC" \
    ./configure --enable-static --disable-shared --disable-docs --disable-deprecated \
    --without-fontconfig --without-gslib --without-x \
    --disable-legacy-support --disable-hdri \
    --disable-openmp --without-modules --enable-zero-configuration \
    --with-zlib --with-png --with-jpeg --with-tiff --with-xml --without-jbig &&\
    make &&\
    make DESTDIR="/root" install

RUN cd /root/usr/local && \
    rm -R bin && \
    llvm-strip-20 -S lib/*.a &&\
    rm -f lib/*.la &&\
    cp -Lv /usr/lib/x86_64-linux-gnu/libjpeg.a lib/ &&\
    tar czvf imagemagick.tar.gz include lib share etc
