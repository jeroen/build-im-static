FROM fedora:38

WORKDIR /root

RUN dnf install -y libcxx-devel clang pkg-config libjpeg-devel libpng-devel libtiff-devel libxml2-devel fontconfig-devel make

# Mirror: https://github.com/ropensci/magick/releases/download/ImageMagick-6.9.13-25/ImageMagick-6.9.13-25.tar.gz
RUN curl -LO https://www.imagemagick.org/archive/ImageMagick-6.9.13-25.tar.gz && \
    tar xf ImageMagick-6.9.13-25.tar.gz && \
    rm ImageMagick-6.9.13-25.tar.gz

RUN cd ImageMagick-6.9.13-25 && \
    CC="clang -std=gnu11 -fPIC" CXX="clang++ -stdlib=libc++ -fPIC" \
    ./configure --enable-static --disable-shared --disable-docs --disable-deprecated \
    --with-fontconfig --with-freetype --without-gslib --without-x \
    --disable-legacy-support --disable-hdri \
    --disable-openmp --without-modules --enable-zero-configuration \
    --with-zlib --with-png --with-jpeg --with-tiff --with-xml \
    --without-zstd --without-jbig --without-webp &&\
    make &&\
    make DESTDIR="/root" install

RUN cd /root/usr/local && \
    rm -R bin && \
    strip -S lib/*.a &&\
    rm -f lib/*.la &&\
    tar czvf imagemagick.tar.gz include lib share etc
