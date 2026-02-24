# #227 - Building in a docker container with only python 3.6 fails [Closed]

> Username: jeking3  
> Created at: 2018-08-11 01:01:20 UTC  
> Updated at: 2018-08-12 22:03:33 UTC  
> Closed at: 2018-08-12 21:21:20 UTC  
> Url: https://github.com/boostorg/python/issues/227  

I am attempting to build all of boost using a docker container that only has Python 3.6 in it, and Boost.Python fails to build:  
```  
gcc.compile.c++ bin.v2/libs/python/build/gcc-7.3/release/threading-multi/list.o  
In file included from ./boost/python/detail/prefix.hpp:13:0,  
                 from ./boost/python/list.hpp:8,  
                 from libs/python/src/list.cpp:5:  
./boost/python/detail/wrap_python.hpp:50:11: fatal error: pyconfig.h: No such file or directory  
 # include <pyconfig.h>  
           ^~~~~~~~~~~~  
compilation terminated.  
  
    "/usr/bin/g++-7"   -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall  -DBOOST_ALL_NO_LIB=1 -DBOOST_PYTHON_SOURCE -DNDEBUG  -I"." -I"/usr/include/python2.7" -c -o "bin.v2/libs/python/build/gcc-7.3/release/threading-multi/list.o" "libs/python/src/list.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/python/build/gcc-7.3/release/threading-multi/list.o...  
gcc.compile.c++ bin.v2/libs/program_options/build/gcc-7.3/release/threading-multi/split.o  
gcc.link.dll bin.v2/libs/program_options/build/gcc-7.3/release/threading-multi/libboost_program_options.so.1.69.0  
common.copy stage/lib/libboost_program_options.so.1.69.0  
...failed updating 1 target...  
...updated 393 targets...  
boost@87988c84883a:/boost$ find / -name pyconfig.h  
/usr/include/x86_64-linux-gnu/python3.6m/pyconfig.h  
/usr/include/python3.6m/pyconfig.h  
```  
  
The docker container can be found at:  
  
https://github.com/boostorg/boost/pull/184  
Using "docker/run.sh ubuntu bionic"  
  
Is this repository compatible with a python 3.6-only environment (no 2.7) from boost jam builds?

---

## Comment 1

> Username: jeking3  
> Created at: 2018-08-11 01:03:54 UTC  
> Url: https://github.com/boostorg/python/issues/227#issuecomment-412240040  

If I install python2.7-dev into the container it seems to resolve the issue, but should that be required?

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2018-08-11 03:03:45 UTC  
> Url: https://github.com/boostorg/python/issues/227#issuecomment-412246650  

Boost.Python should work with Python 3.6. I see two problems with the above:  
* `b2` doesn't correctly detect Python (or at least, some of the corresponding variables, such as the include path)  
* `b2` attempts to build Boost.Python nonetheless  
  
Both of these issues are (arguably) Boost.Build bugs, and thus should be reported there.

---

## Comment 3

> Username: jeking3  
> Created at: 2018-08-12 21:21:20 UTC  
> Url: https://github.com/boostorg/python/issues/227#issuecomment-412372571  

This appears to happen if the system has python2.7 installed but no python2.7-dev, and also python3.6 and python3.6-dev installed.  I added `using python : 3.6 ;` to the `user-config.jam` for the ubuntu bionic docker environment and now it builds properly.

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2018-08-12 21:23:09 UTC  
> Url: https://github.com/boostorg/python/issues/227#issuecomment-412372679  

OK, good. I'd still qualify this as a bug (in Boost.Build), but I'm glad you found a way to unblock you.

---

## Comment 5

> Username: jeking3  
> Created at: 2018-08-12 22:03:33 UTC  
> Url: https://github.com/boostorg/python/issues/227#issuecomment-412375008  

I cannot build libs/python/test with this workaround but I can build boost from the top level.
