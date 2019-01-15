FROM ubuntu:xenial

# These version numbers match the version numbers of the imobiledevice-net NuGet package.
# They are the main version numbers used by libimobiledevice-win32, and this version number
# is consistent across the libplist, libusbmuxd, libimobiledevice and usbmuxd repositories.
ARG LIBIMOBILEDEVICE_WIN32_VERSION=v1.2.72

ENV HOST=aarch64-linux-gnu

RUN apt-get update \
&& apt-get install -y git wget \
&& apt-get install -y autoconf autotools-dev gcc g++ libssl-dev libtool make pkg-config \
&& apt-get install -y crossbuild-essential-arm64 libgnutls28-dev libgcrypt20-dev \
# Delete all the apt list files since they're big and get stale quickly
&& rm -rf /var/lib/apt/lists/*

WORKDIR /src
ENV CFLAGS="-fPIC"

RUN wget https://github.com/libusb/libusb/archive/v1.0.21.tar.gz -O libusb-1.0.21.tar.gz \
&& tar xvf libusb-1.0.21.tar.gz \
&& git clone --branch $LIBIMOBILEDEVICE_WIN32_VERSION https://github.com/libimobiledevice-win32/libplist \
&& git clone --branch msvc-master https://github.com/libimobiledevice-win32/libusbmuxd \
&& git clone --branch $LIBIMOBILEDEVICE_WIN32_VERSION https://github.com/libimobiledevice-win32/libimobiledevice \
&& git clone --branch master-msvc https://github.com/libimobiledevice-win32/usbmuxd

# Running these commands (removing libssl-dev), will trigger a removal of wget as well. So make sure
# any wget-related commands are above this line.
RUN wget http://ports.ubuntu.com/ubuntu-ports/pool/main/o/openssl/libssl1.0-dev_1.0.2g-1ubuntu13.6_arm64.deb -O libssl1.0-dev_1.0.2g-1ubuntu13.6_arm64.deb \
&& wget http://ports.ubuntu.com/ubuntu-ports/pool/main/o/openssl/libssl-dev_1.0.2g-1ubuntu4.14_arm64.deb -O libssl-dev_1.0.2g-1ubuntu4.14_arm64.deb \
&& wget http://ports.ubuntu.com/ubuntu-ports/pool/main/o/openssl/libssl1.0.0_1.0.2g-1ubuntu4.14_arm64.deb -O libssl1.0.0_1.0.2g-1ubuntu4.14_arm64.deb \
&& apt-get remove -y libssl-dev libssl1.0.0 \
&& dpkg --add-architecture arm64

RUN dpkg -i --ignore-depends=libssl1.0.0:arm64,zlib1g-dev:arm64 libssl-dev_1.0.2g-1ubuntu4.14_arm64.deb
RUN dpkg -i --ignore-depends=libssl-dev:arm64 libssl1.0-dev_1.0.2g-1ubuntu13.6_arm64.deb
RUN dpkg -i --ignore-depends=libc6:arm64 libssl1.0.0_1.0.2g-1ubuntu4.14_arm64.deb

WORKDIR /src/libusb-1.0.21
RUN ./autogen.sh enable_udev=no --prefix=/usr --host=$HOST \
&& make \
&& make install \
&& DESTDIR=/out make install

WORKDIR /src/libplist
RUN ./autogen.sh --without-cython --prefix=/usr --host=$HOST \
&& make \
&& make install \
&& DESTDIR=/out make install

WORKDIR /src/libusbmuxd
RUN ./autogen.sh --without-cython --prefix=/usr --host=$HOST \
&& make \
&& make install \
&& DESTDIR=/out make install

WORKDIR /src/libimobiledevice
RUN ./autogen.sh --without-cython --prefix=/usr --host=$HOST --enable-static=no --enable-shared=yes \
&& make \
&& make install \
&& DESTDIR=/out make install

WORKDIR /src/usbmuxd
RUN ./autogen.sh --prefix=/usr --host=$HOST \
&& make \
&& make install \
&& DESTDIR=/out make install

FROM multiarch/ubuntu-core:arm64-xenial

WORKDIR /

RUN apt-get update \
&& apt-get install -y openssl \
# Delete all the apt list files since they're big and get stale quickly
&& rm -rf /var/lib/apt/lists/*

COPY --from=0 /out/usr/ /usr/

# Start the server by default
CMD [ "/usr/sbin/usbmuxd", "-f", "-v", "-w", "-t", "-a" ]

EXPOSE 27015
