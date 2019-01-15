# docker
[![Build Status](https://travis-ci.org/libimobiledevice-win32/docker.svg?branch=master)](https://travis-ci.org/libimobiledevice-win32/docker)

This repository contains a Dockerfile for [usbmuxd](https://github.com/libimobiledevice-win32/usbmuxd). It allows you to run usbmuxd in a Docker container.

The docker container is available as [quamotion/usbmuxd on Docker Hub](https://hub.docker.com/r/quamotion/usbmuxd).

## Requirements
- The container needs extended privileges for USB access
- The host's `/dev/bus/usb` must be mounted on the container

## Usage
To start the usbmuxd server, you can run:

```
docker run --privileged --net host -v /dev/bus/usb:/dev/bus/usb --name usbmuxd quamotion/usbmuxd
```

You'll now have a containerized usbmud running on your server.
