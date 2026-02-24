# #462 - Boost cannot find numpy37 on Dockerized Ubuntu 20.04 [Closed]

> Username: rdebokx  
> Created at: 2021-01-14 14:02:53 UTC  
> Updated at: 2021-02-22 14:24:51 UTC  
> Closed at: 2021-02-22 14:24:50 UTC  
> Url: https://github.com/boostorg/boost/issues/462  

Hi,  
  
I've been trying to get [GP-GOMEA running in a dockerized environment](https://github.com/marcovirgolin/GP-GOMEA/issues/2), which is a project that is built using Boost.  
  
As part of the build process, the source code is compiled by the `g++` compiler, using the flag `-lboost_numpy37`, which fails because (it seems) numpy cannot be found.  
  
I've create the following `Dockerfile` as a minimal test case, which runs into the same problem. I'm a bit of a noob when it comes to Boost and numpy, but from what I found so far on the web, this should work (but it doesn't)  
  
```  
FROM ubuntu:20.04  
  
ENV DEBIAN_FRONTEND noninteractive  
  
RUN apt-get update && apt-get upgrade -y && apt-get clean  
RUN apt-get install -y curl python3.7 python3-dev python3-distutils python3-numpy  
  
# Install latest boost version  
RUN apt-get -y install wget g++  
RUN wget https://dl.bintray.com/boostorg/release/1.75.0/source/boost_1_75_0.tar.bz2 && \  
    tar --bzip2 -xf boost_1_75_0.tar.bz2 && \  
    cd boost_1_75_0 && \  
    # this will generate ./b2  
    ./bootstrap.sh  --prefix=/usr/local && \  
    ./b2 --with=all install && \  
    sh -c 'echo "/usr/local/lib" >> /etc/ld.so.conf.d/local.conf' && \  
    ldconfig  
  
# Check numpy installation  
RUN find / -name "numpy.hpp"  
RUN find / -name "libboost_numpy37.so"  
RUN find / -name "libboost_numpy3.so"  
RUN find / -name "libboost_numpy.so"  
RUN ld -lboost_numpy37 --verbose  
  
```  
  
This eventually results in the following error:  
`The command '/bin/sh -c ld -lboost_numpy37 --verbose' returned a non-zero code: 1`, because it could not find any `libboost_numpy37.so` or `libboost_numpy37.o` file.  
  
See attached log file for the full log.  
  
Did anyone run into this before? Any help is really appreciated.  
  
Cheers.  
[log.txt](https://github.com/boostorg/boost/files/5814822/log.txt)

---

## Comment 1

> Username: mclow  
> Created at: 2021-01-26 14:58:12 UTC  
> Url: https://github.com/boostorg/boost/issues/462#issuecomment-767599921  

A better place for this would be the boost-users mailing list: https://lists.boost.org/mailman/listinfo.cgi/boost-users

---

## Comment 2

> Username: rdebokx  
> Created at: 2021-02-19 10:36:32 UTC  
> Url: https://github.com/boostorg/boost/issues/462#issuecomment-781990330  

Thanks @mclow ! It took some time before I got to this, but currently debugging with the help of users at the mailinglist.  
  
A small update.  
  
I've tried to run this on Ubuntu 18.04 instead, per the suggestion of one of the users.  
DockerFile:   
```  
FROM ubuntu:18.04  
  
ENV DEBIAN_FRONTEND noninteractive  
  
RUN apt-get update && apt-get upgrade -y && apt-get clean  
RUN apt-get install -y curl python3 python3-dev python3-distutils python3-numpy libboost-all-dev  
  
# Check numpy installation  
RUN find / -name libboost_numpy3.so  
RUN find / -name libboost_numpy.so  
RUN ld -lboost_numpy3 --verbose   
```  
  
This eventually fails with the following error(s). See also also attached log.  
```  
//usr/lib/x86_64-linux-gnu/libboost_numpy3.so: undefined reference to `PyExc_ValueError'  
//usr/lib/x86_64-linux-gnu/libboost_python3-py36.so.1.65.1: undefined reference to `PyLong_AsLong'  
//usr/lib/x86_64-linux-gnu/libboost_python3-py36.so.1.65.1: undefined reference to `PyNumber_InPlaceFloorDivide'  
//usr/lib/x86_64-linux-gnu/libboost_python3-py36.so.1.65.1: undefined reference to `PyBool_Type'  
//usr/lib/x86_64-linux-gnu/libboost_python3-py36.so.1.65.1: undefined reference to `PyObject_GetAttr'  
//usr/lib/x86_64-linux-gnu/libboost_python3-py36.so.1.65.1: undefined reference to `PyDict_GetItemString'  
... (many similar errors follow)  
```  
  
[log.txt](https://github.com/boostorg/boost/files/6009223/log.txt)

---

## Comment 3

> Username: rdebokx  
> Created at: 2021-02-22 14:24:50 UTC  
> Url: https://github.com/boostorg/boost/issues/462#issuecomment-783411525  

Unfortunately I didn't get this to run on Ubuntu 20.04, but I did manage to get this running using Python 3.6 on Ubuntu 18.04 using the following Dockerfile:  
  
```  
FROM ubuntu:18.04  
  
ENV DEBIAN_FRONTEND noninteractive  
  
RUN apt-get update && apt-get upgrade -y && apt-get clean  
RUN apt-get install -y curl python3 python3-dev python3-distutils python3-numpy libboost-all-dev  
  
# Check numpy installation  
RUN ld -lpython3.6m -lboost_numpy3 --verbose   
```
