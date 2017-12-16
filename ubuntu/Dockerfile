FROM ubuntu:xenial

RUN apt-get update \
&& apt-get install -y git wget \
&& apt-get install -y autoconf autotools-dev gcc g++ libssl-dev libtool make pkg-config \
# Delete all the apt list files since they're big and get stale quickly
&& rm -rf /var/lib/apt/lists/*

WORKDIR /src

RUN wget https://github.com/libusb/libusb/archive/v1.0.21.tar.gz -O libusb-1.0.21.tar.gz \
&& tar xvf libusb-1.0.21.tar.gz \
&& git clone https://github.com/libimobiledevice-win32/libplist \
&& git clone https://github.com/libimobiledevice-win32/libusbmuxd \
&& git clone https://github.com/libimobiledevice-win32/libimobiledevice \
&& git clone https://github.com/libimobiledevice-win32/usbmuxd

WORKDIR /src/libusb-1.0.21
RUN ./autogen.sh enable_udev=no --prefix=/usr \
&& make \
&& make install \
&& DESTDIR=/out make install

WORKDIR /src/libplist
RUN ./autogen.sh --without-cython --prefix=/usr \
&& make \
&& make install \
&& DESTDIR=/out make install

WORKDIR /src/libusbmuxd
RUN ./autogen.sh --without-cython --prefix=/usr \
&& make \
&& make install \
&& DESTDIR=/out make install

WORKDIR /src/libimobiledevice
RUN ./autogen.sh --without-cython --prefix=/usr \
&& make \
&& make install \
&& DESTDIR=/out make install

WORKDIR /src/usbmuxd
RUN ./autogen.sh --prefix=/usr \
&& make \
&& make install \
&& DESTDIR=/out make install

FROM ubuntu:xenial

WORKDIR /

RUN apt-get update \
&& apt-get install -y libssl \
# Delete all the apt list files since they're big and get stale quickly
&& rm -rf /var/lib/apt/lists/*

COPY --from=0 /out/usr/ /usr/

# Start the server by default
CMD [ "/usr/sbin/usbmuxd", "-f", "-v", "-w", "-t", "-a" ]

EXPOSE 27015
