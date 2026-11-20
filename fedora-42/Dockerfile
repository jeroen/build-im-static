FROM fedora:42

WORKDIR /root

# NB: Fedora builds against legacy libjpeg (libjpeg.so.62)
RUN dnf install -y libcxx-devel clang++ pkg-config libjpeg-devel libpng-devel libtiff-devel libxml2-devel fontconfig-devel awk make cmp

# Upstream archive is no longer available at imagemagick.org/archive, use our mirror
RUN curl -fLO https://github.com/ropensci/magick/releases/download/ImageMagick-6.9.13-25/ImageMagick-6.9.13-25.tar.gz && \
    tar xf ImageMagick-6.9.13-25.tar.gz && \
    rm ImageMagick-6.9.13-25.tar.gz

# Do not use the libxml2 nano FTP/HTTP client: removed from libxml2 2.14/2.15, and magick
# downloads URLs via the curl R package anyway.
RUN sed -i -e "s/defined(LIBXML_FTP_ENABLED)/0/g" -e "s/defined(LIBXML_HTTP_ENABLED)/0/g" ImageMagick-6.9.13-25/coders/url.c

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
