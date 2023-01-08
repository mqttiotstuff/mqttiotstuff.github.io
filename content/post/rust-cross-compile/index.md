+++
title = "Creating cross compile executable with RUST"
description = "Cross compilation using rust for arm architectures"
date = "2023-01-07"
aliases = ["software" ]
author = "Mqtt IOT Stuff"
thumbnail = ""

tags = [
    "software", "rust"
]
categories = [
    "application",
]

+++

Creating executable for small embedded application can be tedious, especially if the plateform is not supporting executing compilation toochains.


# Creating cross executable with RUST

In first place, creating an executable using rust is quite simple the typical steps are :
- use rustup to add the target plateform, using the triplet describing the hardware, and system
- use the `cargo build --target <TRIPLET>  ` command line to build

Thank's to the community , these command line are easy to use and launch.

A more informative article details these paths : [https://kerkour.com/rust-cross-compilation](https://kerkour.com/rust-cross-compilation)

in my use case, i wish to deploy a small rust executable, dealing with mqtt , on an old OrangePI HW, (Armv7 with some floating point hardware support), 
the OS was quite old (a couple of years since setup and not updated).
The compilation with the --target=armv7-unknown-linux-gnuabihf generate the executable on my x64, bu when copied and launched on the Orangepi, the gclibc was too old for the dynlink, and i faced a GLIBC _VERSION_ not found.


## Dealing with the glibc and linkages

docker to the rescue
from the information on the website : [https://capnfabs.net/posts/cross-compiling-rust-apps-linker-shenanigans-multistrap-chroot/](https://capnfabs.net/posts/cross-compiling-rust-apps-linker-shenanigans-multistrap-chroot/)


Dockerfile:
```
FROM rustembedded/cross:armv7-unknown-linux-gnueabihf-0.2.1

COPY rustup.sh /usr/local/bin/rustup.sh
COPY cargo-config /cargo/config

ENV CARGO_HOME=/cargo \
    PATH=/cargo/bin:$PATH \
    USER=root

RUN bash /usr/local/bin/rustup.sh -y && \
    rustup default stable && \
    rustup target add armv7-unknown-linux-gnueabihf && \
    rustup target add armv7-unknown-linux-musleabihf && \
    rustup target add x86_64-unknown-linux-gnu

RUN cargo install cross

# Install multistrap
RUN apt-get update && apt-get install multistrap --assume-yes
# Put the target system here in /sysroot-armhf
RUN mkdir /sysroot-armhf
# We'll explain this in just a second
COPY multistrap-config /
WORKDIR /
RUN multistrap -a armhf -f multistrap-config 

ENV PKG_CONFIG_LIBDIR_armv7_unknown_linux_gnueabihf=/sysroot-armhf/usr/lib/arm-linux-gnueabihf/pkgconfig
ENV PKG_CONFIG_SYSROOT_DIR_armv7_unknown_linux_gnueabihf=/sysroot-armhf



```

multistrap-config

```
use@alexa:~/rsiotmonitor/docker2$ more multistrap-config 
[General]
# target architecture
arch=armhf

directory=/sysroot-armhf

# Don't bother with authentication. I'd love to support this, but
# couldn't get it working! If you know how, please get in touch.
noauth=true
# Unpack the packages
unpack=true
# Tidy up afterwards (makes the container smaller)
cleanup=true
# Both of these refer to the 'Raspbian' stanza, below
bootstrap=raspbian
aptsources=raspbian

[raspbian]
# Required packages for our build
packages=libasound2-dev libdbus-1-dev libssl-dev libc6-dev
source=http://raspbian.raspberrypi.org/raspbian/
# distribution version name
suite=buster

```


cargo-config 
```
[target.armv7-unknown-linux-gnueabihf]
linker = "arm-linux-gnueabihf-gcc"
```



constructing the image :

	docker build -t crossbuild:local .


	docker run -ti --rm -v `pwd`:/code crossbuild:local

	cd /code


	cargo build --target armv7-unknown-linux-gnueabihf

then the binary is copiable and work on the target system :


```
pi@OrangePi:~/tmp$ ll
total 57844
drwxrwxr-x 2 pi pi     4096 janv.  7 17:50 ./
drwxr-xr-x 9 pi pi     4096 janv.  7 12:19 ../
-rwxr-xr-x 1 pi pi 59220272 janv.  7 17:50 rsiotmonitor*
pi@OrangePi:~/tmp$ ldd rsiotmonitor 
	linux-vdso.so.1 =>  (0xbedc6000)
	libgcc_s.so.1 => /lib/arm-linux-gnueabihf/libgcc_s.so.1 (0xb68f4000)
	librt.so.1 => /lib/arm-linux-gnueabihf/librt.so.1 (0xb68de000)
	libpthread.so.0 => /lib/arm-linux-gnueabihf/libpthread.so.0 (0xb68ba000)
	libm.so.6 => /lib/arm-linux-gnueabihf/libm.so.6 (0xb6842000)
	libdl.so.2 => /lib/arm-linux-gnueabihf/libdl.so.2 (0xb682f000)
	libc.so.6 => /lib/arm-linux-gnueabihf/libc.so.6 (0xb6743000)
	/lib/ld-linux-armhf.so.3 (0xb6fba000)
pi@OrangePi:~/tmp$ 

```
binary size 52mb which is a bit large, stripping the symbols, and in release mode, the result is much smaller (8mb)


```
root@335ad79e967c:/code/target/armv7-unknown-linux-gnueabihf/release# ll
total 8708
drwxr-xr-x  7 root root    4096 Jan  7 16:59 ./
drwxr-xr-x  4 root root    4096 Jan  7 16:58 ../
-rw-r--r--  1 root root       0 Jan  7 16:58 .cargo-lock
drwxr-xr-x 68 root root    4096 Jan  7 16:58 .fingerprint/
drwxr-xr-x 11 root root    4096 Jan  7 16:58 build/
drwxr-xr-x  2 root root   20480 Jan  7 16:59 deps/
drwxr-xr-x  2 root root    4096 Jan  7 16:58 examples/
drwxr-xr-x  2 root root    4096 Jan  7 16:58 incremental/
-rwxr-xr-x  2 root root 8861924 Jan  7 16:59 rsiotmonitor*
-rw-r--r--  1 root root      83 Jan  7 16:59 rsiotmonitor.d


```


