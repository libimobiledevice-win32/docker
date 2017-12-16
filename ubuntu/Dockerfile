FROM ubuntu:xenial

RUN apt-get update \
&& apt-get install -y git wget \
&& apt-get install -y autoconf autotools-dev gcc g++ libssl-dev libtool make pkg-config \
# Delete all the apt list files since they're big and get stale quickly
&& rm -rf /var/lib/apt/lists/*

WORKDIR /src

RUN wget https://github.com/libusb/libusb/archive/v1.0.21.tar.gz -O libusb-1.0.21.tar.gz \
&& tar xvf libusb-1.0.21.tar.gz \
&& wget https://github.com/libimobiledevice-win32/libplist/archive/msvc-master.tar.gz -O libplist-msvc-master.tar.gz \
&& tar xvf libplist-msvc-master.tar.gz \
&& wget https://github.com/libimobiledevice-win32/libusbmuxd/archive/msvc-master.tar.gz -O libusbmuxd-msvc-master.tar.gz \
&& tar xvf libusbmuxd-msvc-master.tar.gz \
&& wget https://github.com/libimobiledevice-win32/libimobiledevice/archive/msvc-master.tar.gz -O libimobiledevice-msvc-master.tar.gz \
&& tar xvf libimobiledevice-msvc-master.tar.gz \
&& wget https://github.com/libimobiledevice-win32/usbmuxd/archive/master-msvc.tar.gz -O usbmuxd-master-msvc.tar.gz \
&& tar xvf usbmuxd-master-msvc.tar.gz

WORKDIR /src/libusb-1.0.21
RUN ./autogen.sh enable_udev=no --prefix=/usr \
&& make \
&& make install \
&& DESTDIR=/out make install

WORKDIR /src/libplist-msvc-master
RUN ./autogen.sh --without-cython --prefix=/usr \
&& make \
&& make install \
&& DESTIR=/out make install

WORKDIR /src/libusbmuxd-msvc-master
RUN ./autogen.sh --without-cython --prefix=/usr \
&& make \
&& make install \
&& DESTIR=/out make install

WORKDIR /src/libimobiledevice-msvc-master
RUN ./autogen.sh --without-cython --prefix=/usr \
&& make \
&& make install \
&& DESTIR=/out make install

WORKDIR /src/usbmuxd-master-msvc
RUN ./autogen.sh --without-cython --prefix=/usr \
&& make \
&& make install \
&& DESTIR=/out make install

FROM ubuntu:xenial

WORKDIR /

COPY --from=0 /out/usr/ /usr/

# Start the server by default
CMD [ "/usr/sbin/usbmuxd", "-f", "-v", "-w", "-t", "-a" ]

EXPOSE 27015
