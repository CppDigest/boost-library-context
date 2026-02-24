# #15 - Inconsistent boost cmake file names [Open]

> Username: xnox  
> Created at: 2019-10-24 10:29:28 UTC  
> Updated at: 2019-10-25 23:50:27 UTC  
> Url: https://github.com/boostorg/boost_install/issues/15  

I'm not quite sure what is going on, and I'm not sure if this is a bug in mpi module or the cmake generation. Currently 1.71 build emits:  
  
```  
boost1.71_1.71.0-1_amd64.build:dh_missing: usr/lib/x86_64-linux-gnu/cmake/boost_mpi-1.71.0/libboost_mpi-variant-static.cmake exists in debian/tmp but is not installed to anywhere  
boost1.71_1.71.0-1_amd64.build:dh_missing: usr/lib/x86_64-linux-gnu/cmake/boost_mpi-1.71.0/boost_mpi-config-version.cmake exists in debian/tmp but is not installed to anywhere  
boost1.71_1.71.0-1_amd64.build:dh_missing: usr/lib/x86_64-linux-gnu/cmake/boost_mpi-1.71.0/boost_mpi-config.cmake exists in debian/tmp but is not installed to anywhere  
boost1.71_1.71.0-1_amd64.build:dh_missing: usr/lib/x86_64-linux-gnu/cmake/boost_mpi-1.71.0/libboost_mpi-variant-shared.cmake exists in debian/tmp but is not installed to anywhere  
boost1.71_1.71.0-1_amd64.build:dh_missing: usr/lib/x86_64-linux-gnu/cmake/mpi-1.71.0/mpi-config.cmake exists in debian/tmp but is not installed to anywhere  
boost1.71_1.71.0-1_amd64.build:dh_missing: usr/lib/x86_64-linux-gnu/cmake/mpi-1.71.0/mpi-config-version.cmake exists in debian/tmp but is not installed to anywhere  
boost1.71_1.71.0-1_amd64.build:dh_missing: usr/lib/x86_64-linux-gnu/cmake/mpi-1.71.0/libmpi-variant-shared-py3.8.cmake exists in debian/tmp but is not installed to anywhere  
boost1.71_1.71.0-1_amd64.build:dh_missing: usr/lib/x86_64-linux-gnu/cmake/mpi-1.71.0/libmpi-variant-shared-py3.7.cmake exists in debian/tmp but is not installed to anywhere  
boost1.71_1.71.0-1_amd64.build:dh_missing: usr/lib/x86_64-linux-gnu/cmake/mpi-1.71.0/libmpi-variant-static-py3.7.cmake exists in debian/tmp but is not installed to anywhere  
boost1.71_1.71.0-1_amd64.build:dh_missing: usr/lib/x86_64-linux-gnu/cmake/mpi-1.71.0/libmpi-variant-static-py3.8.cmake exists in debian/tmp but is not installed to anywhere  
boost1.71_1.71.0-1_amd64.build:dh_missing: usr/lib/x86_64-linux-gnu/cmake/boost_mpi_python-1.71.0/boost_mpi_python-config-version.cmake exists in debian/tmp but is not installed to anywhere  
boost1.71_1.71.0-1_amd64.build:dh_missing: usr/lib/x86_64-linux-gnu/cmake/boost_mpi_python-1.71.0/libboost_mpi_python-variant-static-py3.8.cmake exists in debian/tmp but is not installed to anywhere  
boost1.71_1.71.0-1_amd64.build:dh_missing: usr/lib/x86_64-linux-gnu/cmake/boost_mpi_python-1.71.0/libboost_mpi_python-variant-shared-py3.8.cmake exists in debian/tmp but is not installed to anywhere  
boost1.71_1.71.0-1_amd64.build:dh_missing: usr/lib/x86_64-linux-gnu/cmake/boost_mpi_python-1.71.0/libboost_mpi_python-variant-static-py3.7.cmake exists in debian/tmp but is not installed to anywhere  
boost1.71_1.71.0-1_amd64.build:dh_missing: usr/lib/x86_64-linux-gnu/cmake/boost_mpi_python-1.71.0/libboost_mpi_python-variant-shared-py3.7.cmake exists in debian/tmp but is not installed to anywhere  
boost1.71_1.71.0-1_amd64.build:dh_missing: usr/lib/x86_64-linux-gnu/cmake/boost_mpi_python-1.71.0/boost_mpi_python-config.cmake exists in debian/tmp but is not installed to anywhere  
```  
  
And I can understand `boost_mpi-1.71.0/` and the `boost_mpi_python-1.71.0` but i am very confused about the `mpi-1.71.0/` as to what it is, and which library I should package it with: boost_mpi or boost_mpi_python or if it shouldn't exist at all!  
  
Is it like cmake files generated for the compiled python mpi extension? ie. the one that is installed on Debian systems as:  
```  
-rw-r--r-- root/root       264 2019-03-12 03:44 ./usr/lib/python3/dist-packages/boost/__init__.py  
-rw-r--r-- root/root    497112 2019-03-12 03:44 ./usr/lib/python3/dist-packages/boost/mpi.cpython-37m-x86_64-linux-gnu.so  
-rw-r--r-- root/root    501208 2019-03-12 03:44 ./usr/lib/python3/dist-packages/boost/mpi.cpython-38-x86_64-linux-gnu.so  
```  
  
Frankly, I don't understand cmake files for boost here =)

---

## Comment 1

> Username: pdimov  
> Created at: 2019-10-24 11:59:15 UTC  
> Url: https://github.com/boostorg/boost_install/issues/15#issuecomment-545884389  

Yes, it looks like Boost.MPI installs a Python extension called `mpi`: https://github.com/boostorg/mpi/blob/c7fdb33cca09d7248826a340ff40be5eec1e36af/build/Jamfile.v2#L173  
  
I agree that installing a CMake configuration for a package called `mpi` is not something we should do. Not sure where and how to fix that properly.

---

## Comment 2

> Username: xnox  
> Created at: 2019-10-24 13:27:26 UTC  
> Url: https://github.com/boostorg/boost_install/issues/15#issuecomment-545917364  

I also wee a few other "weird" cmake files:  
  
- boost_stacktrace_windbg-1.71.0 (nothing like this exists on my linux build)  
- boost_stacktrace_windbg_cached-1.71.0  (nothing like this exists on my linux build)  
- boost_fiber_numa-1.71.0 (in addition to the just boost_fiber, no -numa library exists)  
- boost_math-1.71.0 (in addition to _c99|_ctr1|etc)  
- boost_headers-1.71.0 (no idea what it means..... just header locations without a library?!)

---

## Comment 3

> Username: pdimov  
> Created at: 2019-10-24 16:33:38 UTC  
> Url: https://github.com/boostorg/boost_install/issues/15#issuecomment-545999930  

The first three are conditionally-built libraries. For complicated Boost.Build reasons the decision to create the CMake config files is made at configure time, and the conditions that govern whether the libraries are built are evaluated later, at build time. Maybe this can be fixed, but it exceeds my Boost.Build abilities at present. :-)  
  
`boost_math` is my fault (https://github.com/boostorg/math/commit/8d2c59c8614f8ad5861f342d4ee3d41a6c456d7b) and we should probably remove it as it should be no longer required.  
  
`boost_headers` is important and you do need it. It declares the `Boost::headers` target that represents all header-only libraries and is the equivalent of the `Boost::boost` target from FindBoost.

---

## Comment 4

> Username: pdimov  
> Created at: 2019-10-24 18:22:10 UTC  
> Url: https://github.com/boostorg/boost_install/issues/15#issuecomment-546042084  

https://github.com/boostorg/math/pull/266

---

## Comment 5

> Username: xnox  
> Created at: 2019-10-25 23:27:36 UTC  
> Updated at: 2019-10-25 23:27:50 UTC  
> Url: https://github.com/boostorg/boost_install/issues/15#issuecomment-546543341  

Ok, I shall install boost_headers into the main libboost-dev package next to the rest of headers.  
  
I will ignore / purge the conditional libs.  
  
Now, how do I use these CMake files in cmake projects? I.e. I want to convert some of cmake project which currently uses boost-python to use these new fancy cmake files. How can I do that?  
  
Are there docs on how to use these cmake things?

---

## Comment 6

> Username: xnox  
> Created at: 2019-10-25 23:28:53 UTC  
> Url: https://github.com/boostorg/boost_install/issues/15#issuecomment-546543549  

Sure, i don't care about _configuring_ and _building_ them, but they shouldn't be _installed_ (which happens after building) thus should be like present in the builddir, but not installed into the prefix?

---

## Comment 7

> Username: pdimov  
> Created at: 2019-10-25 23:41:18 UTC  
> Url: https://github.com/boostorg/boost_install/issues/15#issuecomment-546545360  

>Now, how do I use these CMake files in cmake projects?  
  
`find_package(Boost)` will automatically use them if they are installed somewhere in the CMake search path.

---

## Comment 8

> Username: pdimov  
> Created at: 2019-10-25 23:50:27 UTC  
> Url: https://github.com/boostorg/boost_install/issues/15#issuecomment-546546538  

Looking at the Qt5 package, looks like it installs the .cmake configs in `/usr/lib/x86_64-linux-gnu/cmake`, which is probably where the Boost ones should go, too.
