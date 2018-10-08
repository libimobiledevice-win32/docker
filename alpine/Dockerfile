FROM alpine:3.6

# These version numbers match the version numbers of the imobiledevice-net NuGet package.
# They are the main version numbers used by libimobiledevice-win32, and this version number
# is consistent across the libplist, libusbmuxd, libimobiledevice and usbmuxd repositories.
ARG LIBIMOBILEDEVICE_WIN32_VERSION=v1.2.60

RUN apk --no-cache add  automake autoconf build-base libtool libusb-dev openssl-dev git

WORKDIR /src

RUN git clone --branch $LIBIMOBILEDEVICE_WIN32_VERSION https://github.com/libimobiledevice-win32/libplist \
&& git clone --branch $LIBIMOBILEDEVICE_WIN32_VERSION https://github.com/libimobiledevice-win32/libusbmuxd \
&& git clone --branch $LIBIMOBILEDEVICE_WIN32_VERSION https://github.com/libimobiledevice-win32/libimobiledevice \
&& git clone --branch $LIBIMOBILEDEVICE_WIN32_VERSION https://github.com/libimobiledevice-win32/usbmuxd

WORKDIR /src/libplist
RUN ./autogen.sh --without-cython --prefix=/usr \
&& make \
&& make install \
&& make install DESTDIR=/out

WORKDIR /src/libusbmuxd
RUN ./autogen.sh --without-cython --prefix=/usr \
&& make \
&& make install \
&& make install DESTDIR=/out

WORKDIR /src/libimobiledevice
RUN ./autogen.sh --without-cython --prefix=/usr \
&& make \
&& make install \
&& make install DESTDIR=/out

WORKDIR /src/usbmuxd
RUN ./autogen.sh --prefix=/usr \
&& make \
&& make install \
&& make install DESTDIR=/out

FROM alpine:3.6

WORKDIR /

RUN apk --no-cache add  libusb openssl

COPY --from=0 /out/usr/ /usr/

# Start the server by default
CMD [ "/usr/sbin/usbmuxd", "-f", "-v", "-w", "-t", "-a" ]

EXPOSE 27015
