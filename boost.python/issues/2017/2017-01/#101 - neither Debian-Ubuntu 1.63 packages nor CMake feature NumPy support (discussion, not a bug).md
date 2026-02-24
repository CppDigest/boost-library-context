# #101 - neither Debian/Ubuntu 1.63 packages nor CMake feature NumPy support (discussion, not a bug) [Open]

> Username: slayoo  
> Created at: 2017-01-31 12:01:37 UTC  
> Updated at: 2017-03-29 11:31:26 UTC  
> Url: https://github.com/boostorg/python/issues/101  

Excuse me for posting here as it is likely a packaging problem, yet I thought I could get some hints here before proceeding with a Debian bug report.  
  
Trying to use the new NumPy support, I came up with the following partial hello world code which compiles, links but fails with an undefined symbol error at runtime:  
  
*hello.cpp*:  
```C++  
#include <boost/python/numpy.hpp>  
  
void init()  
{  
  Py_Initialize();  
  boost::python::numpy::initialize();  
}  
  
BOOST_PYTHON_MODULE(hello)  
{  
}  
```  
  
*CMakeLists.txt*:  
```CMake  
project(hello CXX)  
cmake_minimum_required(VERSION 3.7)  
  
add_library(hello SHARED hello.cpp)  
set_target_properties(hello PROPERTIES SUFFIX ".so") # e.g. Mac defaults to .dylib which is not looked for by Python  
  
# informing the Python bindings where to find Python  
find_package(PythonInterp REQUIRED)  
find_package(PythonLibs ${PYTHON_VERSION_STRING} EXACT REQUIRED)  
target_include_directories(hello PUBLIC ${PYTHON_INCLUDE_DIRS})  
target_link_libraries(hello ${PYTHON_LIBRARIES})  
  
  
# informing the Python bindings where to find Boost.Python  
# NumPy support was introduced in 1.63  
find_package(Boost 1.63 COMPONENTS python-py${PYTHON_VERSION_MAJOR}${PYTHON_VERSION_MINOR} QUIET REQUIRED)  
target_link_libraries(hello ${Boost_LIBRARIES})  
target_include_directories(hello PUBLIC ${Boost_INCLUDE_DIRS})  
```  
  
Here's what happens on a all-standard Ubuntu zesty installation with the Debian/Ubuntu 1.63 packages:  
  
```  
$ mkdir build  
$ cd build/  
$ cmake ..  
-- The CXX compiler identification is GNU 4.9.2  
-- Check for working CXX compiler: /usr/bin/c++  
-- Check for working CXX compiler: /usr/bin/c++ -- works  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Found PythonInterp: /usr/bin/python (found version "2.7.13")  
-- Found PythonLibs: /usr/lib/x86_64-linux-gnu/libpython2.7.so (found suitable exact version "2.7.13")  
CMake Warning at /usr/share/cmake-3.7/Modules/FindBoost.cmake:1534 (message):  
  No header defined for python-py27; skipping header check  
Call Stack (most recent call first):  
  CMakeLists.txt:16 (find_package)  
  
  
-- Configuring done  
-- Generating done  
-- Build files have been written to: /home/vagrant/temp/build  
$ make  
Scanning dependencies of target hello  
[ 50%] Building CXX object CMakeFiles/hello.dir/hello.cpp.o  
[100%] Linking CXX shared library libhello.so  
[100%] Built target hello  
$ python  
Python 2.7.13 (default, Jan 19 2017, 14:48:08)  
[GCC 6.3.0 20170118] on linux2  
Type "help", "copyright", "credits" or "license" for more information.  
>>> import libhello  
Traceback (most recent call last):  
  File "<stdin>", line 1, in <module>  
ImportError: ./libhello.so: undefined symbol: _ZN5boost6python5numpy10initializeEb  
>>>  
```  
  
Also:  
```  
$ ldd libhello.so  
        linux-vdso.so.1 =>  (0x00007ffd301d5000)  
        libpython2.7.so.1.0 => /usr/lib/x86_64-linux-gnu/libpython2.7.so.1.0 (0x00007f2c04b3b000)  
        libboost_python-py27.so.1.63.0 => /usr/lib/x86_64-linux-gnu/libboost_python-py27.so.1.63.0 (0x00007f2c048f2000)  
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f2c0452b000)  
        libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f2c0430d000)  
        libz.so.1 => /lib/x86_64-linux-gnu/libz.so.1 (0x00007f2c040f2000)  
        libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f2c03eec000)  
        libutil.so.1 => /lib/x86_64-linux-gnu/libutil.so.1 (0x00007f2c03ce9000)  
        libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007f2c039e0000)  
        libstdc++.so.6 => /usr/lib/x86_64-linux-gnu/libstdc++.so.6 (0x00007f2c03658000)  
        libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1 (0x00007f2c03441000)  
        /lib64/ld-linux-x86-64.so.2 (0x00007f2c052de000)  
```  
  
```  
$ ls -1 /usr/lib/x86_64-linux-gnu/libboost_python*  
/usr/lib/x86_64-linux-gnu/libboost_python.a  
/usr/lib/x86_64-linux-gnu/libboost_python-py27.a  
/usr/lib/x86_64-linux-gnu/libboost_python-py27.so  
/usr/lib/x86_64-linux-gnu/libboost_python-py27.so.1.63.0  
/usr/lib/x86_64-linux-gnu/libboost_python-py35.a  
/usr/lib/x86_64-linux-gnu/libboost_python-py35.so  
/usr/lib/x86_64-linux-gnu/libboost_python-py35.so.1.63.0  
/usr/lib/x86_64-linux-gnu/libboost_python.so  
```  
  
```  
$ nm /usr/lib/x86_64-linux-gnu/libboost_python*.a | grep numpy | wc -l  
0  
```  
  
Thanks in advance for any hints

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-01-31 12:49:51 UTC  
> Url: https://github.com/boostorg/python/issues/101#issuecomment-276354843  

It looks like you are not linking to libboost_numpy.so. I don't fully understand the cmake logic you pasted, but I wonder where the `${Boost_LIBRARIES}` variable is defined, as I suspect that's where that library should appear.  
(Yes, it's entirely plausible that distributors haven't noticed the new NumPy support in Boost.Python, and thus haven't adjusted their own build logic. In fact, I had hoped that downstream packaging would separate the NumPy additions from the original Boost.Python support, as the latter is still independent of NumPy, and thus can be installed without it, while the Boost.NumPy bits are not.)

---

## Comment 2

> Username: slayoo  
> Created at: 2017-01-31 13:10:48 UTC  
> Url: https://github.com/boostorg/python/issues/101#issuecomment-276358910  

Thanks!  
The Debian package file list does not contain the _numpy.so:  
https://packages.debian.org/sid/amd64/libboost-python1.63-dev/filelist  
  
I haven't found any mentions of a new _numpy package.  
I've just reported it on Debian tracker (and will rename this issue so it mentions Debian/Ubuntu package):  
https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=853734

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2017-01-31 13:12:22 UTC  
> Url: https://github.com/boostorg/python/issues/101#issuecomment-276359204  

Great, thanks. Let me know if there is anything I can do to help.

---

## Comment 4

> Username: slayoo  
> Created at: 2017-01-31 13:22:11 UTC  
> Url: https://github.com/boostorg/python/issues/101#issuecomment-276361228  

I've also submitted a bug report to CMake: https://gitlab.kitware.com/cmake/cmake/issues/16612

---

## Comment 5

> Username: rleigh-codelibre  
> Created at: 2017-01-31 19:43:09 UTC  
> Updated at: 2017-01-31 19:46:04 UTC  
> Url: https://github.com/boostorg/python/issues/101#issuecomment-276469862  

Hi, I'm the cmake FindBoost maintainer.  This wasn't picked up because the [automated dependency analyser](https://gitlab.kitware.com/cmake/cmake/blob/master/Utilities/Scripts/BoostScanDeps.cmake) didn't pick up `boost_numpy`.  There are two reasons for this:  
  
- The `boost/python/numpy` headers are missing the appropriate autolink information.  Is this intentional, or a simple omission?  
- It also needs special-casing in the scanner as we already did for the python MPI library  
  
The former is something which you likely need to add yourselves.  The latter I will take care of.  
  
Just as an aside, if Boost could export the dependency information appropriately, it would make supporting Boost on the CMake side vastly easier.  We currently hardcode the full dependency information for every release back to 1.33 due to it not being provided.  If Boost were to provide CMake configuration directly, or in another format we could automatically process, it would make end use of the library significantly simpler.  
  
Kind regards,  
Roger

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2017-01-31 19:58:36 UTC  
> Url: https://github.com/boostorg/python/issues/101#issuecomment-276474300  

I have no idea what "auto-linking" is. Can you elaborate a bit ? Thanks.

---

## Comment 7

> Username: rleigh-codelibre  
> Created at: 2017-01-31 20:05:00 UTC  
> Updated at: 2017-01-31 20:11:56 UTC  
> Url: https://github.com/boostorg/python/issues/101#issuecomment-276476086  

@stefanseefeld Run `grep -R BOOST_LIB_NAME` over the boost headers, or for a specific example please look at `boost/python/detail/config.hpp` and `boost/mpi/python/config.hpp`.  The `boost/python/numpy` headers all need a dependency upon such a header; you would need to create one specifically for the `python_numpy` component such as `boost/python/numpy/config.hpp`, and then have each numpy header with a specific dependency upon symbols in the library depend upon it (or do it in all of them to be absolutely safe).  This will trigger automatic linking with the library when the header is `#include`d.  
  
auto-linking is something which only works in practice on Windows, since it uses compiler-specific pragmas, but since it's the only machine-readable source of library dependency information we use it out of necessity.  We would be more than happy to switch to a better source of dependency information. Right now, we have to recursively parse every boost header to find all the direct and indirect library dependencies and construct a full inter- and intra-module dependency graph from that.  In practice, cmake doesn't use the auto-link mechanism on Windows; it uses its dependency information on Windows and all other platforms just as for any other library.

---

## Comment 8

> Username: stefanseefeld  
> Created at: 2017-02-15 16:57:34 UTC  
> Url: https://github.com/boostorg/python/issues/101#issuecomment-280069411  

I just merged a PR to the `develop` branch that should fix this (https://github.com/boostorg/python/commit/bd7b8ecba5c211c3361c4a9af82a7a527bed384e).

---

## Comment 9

> Username: slayoo  
> Created at: 2017-02-16 08:30:26 UTC  
> Url: https://github.com/boostorg/python/issues/101#issuecomment-280265888  

Thanks! I will post updates to the linked CMake and Debian tickets.

---

## Comment 10

> Username: slayoo  
> Created at: 2017-03-29 11:31:26 UTC  
> Url: https://github.com/boostorg/python/issues/101#issuecomment-290062276  

Relevant merge request in CMake: https://gitlab.kitware.com/cmake/cmake/merge_requests/629
