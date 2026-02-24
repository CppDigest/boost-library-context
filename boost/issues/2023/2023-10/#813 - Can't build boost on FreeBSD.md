# #813 - Can't build boost on FreeBSD [Open]

> Username: hellium6  
> Created at: 2023-10-07 23:20:34 UTC  
> Updated at: 2023-10-07 23:20:34 UTC  
> Url: https://github.com/boostorg/boost/issues/813  

Trying to build boost from master on FreeBSD 12.4. But failed so far.  
  
System info:  
  
```sh  
$ uname -mrs  
FreeBSD 12.4-RELEASE i386  
# dmesg | grep CPU  
CPU: Intel(R) Core(TM) i3 CPU       M 330  @ 2.13GHz (2128.05-MHz 686-class CPU)  
FreeBSD/SMP: Multiprocessor System Detected: 4 CPUs  
cpu0: <ACPI CPU> on acpi0  
# dmesg | grep memory.*=  
real memory  = 4294967296 (4096 MB)  
avail memory = 3048796160 (2907 MB)]  
$ echo $SHELL  
/usr/local/bin/bash  
```  
  
Preparing:  
  
```sh  
$ sudo pkg install -y gcc11 python boost-build  
$ python --version  
Python 3.9.18  
$ b2 --version  
B2 4.9-git  
  
$ gcc11 --version  
gcc11 (FreeBSD Ports Collection) 11.3.0  
Copyright (C) 2021 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
$ grep -q '^using gcc : boost ' /etc/site-config.jam || ( sudo touch /etc/site-config.jam && echo 'using gcc : boost : g++11 : <compileflags>-Wl,-rpath=/usr/local/lib/gcc11 <cxxflags>-std=c++14 ;' | sudo tee -a /etc/site-config.jam )  
$ git clone --depth 1 https://github.com/boostorg/boost && cd boost  
$ git submodule update --init --recursive  
$ git log --oneline  
be1e29c (grafted, HEAD -> master, origin/master, origin/HEAD) Update core from master  
$ ./bootstrap.sh  
```  
  
Building:  
  
```sh  
$ b2 gcc-boost 2>&1 | tee b2.log  
```  
  
Ends with something like:  
  
```  
...  
common.copy /path/to/repos/boost/stage/lib/libboost_wave.a  
...failed updating 252 targets...  
...skipped 60 targets...  
...updated 2153 targets...  
```  
  
Tried with gcc13 (13.1.0) too and the results are similar.  
  
[b2-gcc11.log](https://github.com/boostorg/boost/files/12839419/b2-gcc11.log)  
[b2-gcc13.log](https://github.com/boostorg/boost/files/12839420/b2-gcc13.log)  
  
Tried applying patches from [FreeBSD port](https://github.com/freebsd/freebsd-ports/tree/main/devel/boost-libs/files) but didn't work either.
