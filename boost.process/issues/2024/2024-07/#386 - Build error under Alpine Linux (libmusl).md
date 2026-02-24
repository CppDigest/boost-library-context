# #386 - Build error under Alpine Linux (libmusl) [Closed]

> Username: anarthal  
> Created at: 2024-07-16 10:01:42 UTC  
> Updated at: 2024-07-19 01:17:22 UTC  
> Closed at: 2024-07-19 01:17:21 UTC  
> Url: https://github.com/boostorg/process/issues/386  

It looks like it's assuming the C library in Linux is always glibc, which is not the case in Alpine.  
  
This is the failure:  
  
```  
#22 292.7 libs/process/src/posix/close_handles.cpp:50:10: fatal error: gnu/libc-version.h: No such file or directory  
#22 292.7    50 | #include <gnu/libc-version.h>  
#22 292.7       |          ^~~~~~~~~~~~~~~~~~~~  
#22 292.7 compilation terminated.  
#22 294.7   
#22 294.7     "g++"   -std=c++23 -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_PROCESS_DYN_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DNDEBUG   -I"."  -c -o "bin.v2/libs/process/build/gcc-13/release/x86_64/cxxstd-23-iso/threading-multi/visibility-hidden/posix/close_handles.o" "libs/process/src/posix/close_handles.cpp"  
#22 294.7   
#22 294.7 ...failed gcc.compile.c++ bin.v2/libs/process/build/gcc-13/release/x86_64/cxxstd-23-iso/threading-multi/visibility-hidden/posix/close_handles.o...  
```  
  
You can reproduce with the following Dockerfile:  
  
```  
#  
# Build the server  
#  
FROM alpine:3.20.1 AS server-builder-base  
  
# Packages  
RUN apk update && \  
    apk add --no-cache \  
        g++ \  
        cmake \  
        ninja \  
        openssl-dev \  
        icu-dev \  
        git \  
        linux-headers \  
        wget \  
        tar  
  
RUN wget -q https://archives.boost.io/beta/1.86.0.beta1/source/boost_1_86_0_b1_rc1.tar.gz  
RUN tar -xf boost_1_86_0_b1_rc1.tar.gz  
WORKDIR /boost_1_86_0  
RUN ./bootstrap.sh  
RUN ./b2 cxxstd=23 variant=release  --prefix=/opt/boost install  
```
