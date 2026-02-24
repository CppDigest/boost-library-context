# #48 Fix mpi-python component compilation. [Merged]

> Username: Lastique  
> Created at: 2017-04-20 21:05:54 UTC  
> Updated at: 2017-07-21 08:27:20 UTC  
> Merged at: 2017-07-20 22:34:56 UTC  
> Closed at: 2017-07-20 22:34:56 UTC  
> Url: https://github.com/boostorg/mpi/pull/48  

This fixes Boost.MPI 1.64 compilation:  
  
```  
gcc.compile.c++ bin.v2/libs/mpi/build/gcc-6.3.0/release/debug-symbols-on/threading-multi/python/serialize.o  
In file included from ./boost/python/detail/prefix.hpp:13:0,  
                 from ./boost/python/ssize_t.hpp:9,  
                 from ./boost/python/object.hpp:8,  
                 from ./boost/mpi/python/serialize.hpp:25,  
                 from libs/mpi/src/python/serialize.cpp:13:  
./boost/python/detail/wrap_python.hpp:50:23: fatal error: pyconfig.h: No such file or directory  
 # include <pyconfig.h>  
                       ^  
compilation terminated.  
      
    "g++"  -ftemplate-depth-128 -O3 -finline-functions -Wno-inline -Wall -g -pthread -fPIC -m64 -std=gnu++0x -DBOOST_ALL_NO_LIB=1 -DBOOST_MPI_DYN_LINK=1 -DBOOST_MPI_PYTHON_DYN_LINK=1 -DBOOST_MPI_PYTHON_SOURCE=1 -DBOOST_PYTHON_DYN_LINK=1 -DNDEBUG  -I"." -I"/usr/lib/x86_64-linux-gnu/ope  
      
...failed gcc.compile.c++ bin.v2/libs/mpi/build/gcc-6.3.0/release/debug-symbols-on/threading-multi/python/serialize.o...  
```  
  
Make mpi-python compilation and naming consistent with Boost.Python:  
  
- Add <python> property requirement to the components dependent on Python.  
  This ensures that Boost.Build passes include and library paths for the  
  selected Python version while building these components. This fixes the  
  build error of pyconfig.h not found.  
  
- Add <python-debugging> property handling, to follow Boost.Python practice.  
  This should ensure that correct Python headers and library are used  
  by Boost.Python depending on this property.  
  
- Add suffix 3 to the library name when compiled against Python 3.x. This  
  follows Boost.Python practice. The MPI plugin for Python is still named  
  mpi (without suffix) as it may be visible to python programs. Users are  
  expected to build into separate directories when plugins for different  
  Python versions are required.

---

## Comment 1

> Username: aminiussi  
> Created_at: 2017-07-21 08:21:24 UTC  
> Url: https://github.com/boostorg/mpi/pull/48#issuecomment-316937604  

I'd like to merge it directly to master for 1.65, but I'm not familiar with the mpi python biding.  
Do you know how to run the python related regression tests ?

---

## Comment 2

> Username: Lastique  
> Created_at: 2017-07-21 08:27:20 UTC  
> Url: https://github.com/boostorg/mpi/pull/48#issuecomment-316938951  

Sorry, I don't know. There isn't any project in `libs/mpi/test/python` and `libs/mpi/test/Jamfile.v2` doesn't reference those tests.

---
