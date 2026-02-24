# #333 - custom build for Storm, is it ok? [Open]

> Username: xhajnal  
> Created at: 2020-12-14 16:10:32 UTC  
> Updated at: 2022-06-20 08:29:31 UTC  
> Url: https://github.com/boostorg/python/issues/333  

While Installing Storm using these steps to custom build boost (see last section)  
https://www.stormchecker.org/documentation/obtain-storm/dependencies.html  
  
I have obtained not obvious result whether the installation succeeded or not:  
If not, can you suggest a fix, please? Thanks.  
  
		$ wget https://sourceforge.net/projects/boost/files/boost/1.63.0/boost_1_63_0.tar.gz  
		$ tar -xzf boost_1_63_0.tar.gz  
		$ cd boost_1_63_0  
		$ ./bootstrap.sh  
		Building Boost.Build engine with toolset gcc... tools/build/src/engine/bin.linuxx86_64/b2  
		Detecting Python version... 3.8  
		Detecting Python root... /home/matej/anaconda3/envs/py38  
		Unicode/ICU support for Boost.Regex?... /usr  
		Backing up existing Boost.Build configuration in project-config.jam.2  
		Generating Boost.Build configuration in project-config.jam...  
  
		Bootstrapping is done. To build, run:  
  
		    ./b2  
		      
		To adjust configuration, edit 'project-config.jam'.  
		Further information:  
  
		   - Command line help:  
		     ./b2 --help  
		       
		   - Getting started guide:   
		     http://www.boost.org/more/getting_started/unix-variants.html  
		       
		   - Boost.Build documentation:  
		     http://www.boost.org/build/doc/html/index.html  
		$ ./bjam  
		Performing configuration checks  
  
		    - 32-bit                   : no  (cached)  
		    - 64-bit                   : yes (cached)  
		    - arm                      : no  (cached)  
		    - mips1                    : no  (cached)  
		    - power                    : no  (cached)  
		    - sparc                    : no  (cached)  
		    - x86                      : yes (cached)  
  
		Building the Boost C++ Libraries.  
  
  
		    - symlinks supported       : yes (cached)  
		    - C++11 mutex              : yes (cached)  
		    - lockfree boost::atomic_flag : yes (cached)  
		    - Boost.Config Feature Check: cxx11_auto_declarations : yes (cached)  
		    - Boost.Config Feature Check: cxx11_constexpr : yes (cached)  
		    - Boost.Config Feature Check: cxx11_defaulted_functions : yes (cached)  
		    - Boost.Config Feature Check: cxx11_final : yes (cached)  
		    - Boost.Config Feature Check: cxx11_hdr_mutex : yes (cached)  
		    - Boost.Config Feature Check: cxx11_hdr_tuple : yes (cached)  
		    - Boost.Config Feature Check: cxx11_lambdas : yes (cached)  
		    - Boost.Config Feature Check: cxx11_noexcept : yes (cached)  
		    - Boost.Config Feature Check: cxx11_nullptr : yes (cached)  
		    - Boost.Config Feature Check: cxx11_rvalue_references : yes (cached)  
		    - Boost.Config Feature Check: cxx11_template_aliases : yes (cached)  
		    - Boost.Config Feature Check: cxx11_thread_local : yes (cached)  
		    - Boost.Config Feature Check: cxx11_variadic_templates : yes (cached)  
		    - has_icu builds           : yes (cached)  
		warning: Graph library does not contain MPI-based parallel components.  
		note: to enable them, add "using mpi ;" to your user-config.jam  
		    - zlib                     : yes (cached)  
		    - bzip2                    : yes (cached)  
		    - iconv (libc)             : yes (cached)  
		    - icu                      : yes (cached)  
		    - native-atomic-int32-supported : yes (cached)  
		    - native-syslog-supported  : yes (cached)  
		    - pthread-supports-robust-mutexes : yes (cached)  
		    - compiler-supports-visibility : yes (cached)  
		    - compiler-supports-ssse3  : yes (cached)  
		    - compiler-supports-avx2   : yes (cached)  
		    - gcc visibility           : yes (cached)  
		    - long double support      : yes (cached)  
		warning: skipping optional Message Passing Interface (MPI) library.  
		note: to enable MPI support, add "using mpi ;" to user-config.jam.  
		note: to suppress this message, pass "--without-mpi" to bjam.  
		note: otherwise, you can safely ignore this message.  
		    - zlib                     : yes (cached)  
		    - bzip2                    : yes (cached)  
  
		Component configuration:  
  
		    - atomic                   : building  
		    - chrono                   : building  
		    - container                : building  
		    - context                  : building  
		    - coroutine                : building  
		    - coroutine2               : building  
		    - date_time                : building  
		    - exception                : building  
		    - fiber                    : building  
		    - filesystem               : building  
		    - graph                    : building  
		    - graph_parallel           : building  
		    - iostreams                : building  
		    - locale                   : building  
		    - log                      : building  
		    - math                     : building  
		    - metaparse                : building  
		    - mpi                      : building  
		    - program_options          : building  
		    - python                   : building  
		    - random                   : building  
		    - regex                    : building  
		    - serialization            : building  
		    - signals                  : building  
		    - system                   : building  
		    - test                     : building  
		    - thread                   : building  
		    - timer                    : building  
		    - type_erasure             : building  
		    - wave                     : building  
  
		...patience...  
		...patience...  
		...patience...  
		...patience...  
		...patience...  
		...patience...  
		...found 16129 targets...  
		...updating 20 targets...  
		gcc.compile.c++ bin.v2/libs/python/build/gcc-7/release/link-static/threading-multi/converter/builtin_converters.o  
		libs/python/src/converter/builtin_converters.cpp: In function ‘void* boost::python::converter::{anonymous}::convert_to_cstring(PyObject*)’:  
		libs/python/src/converter/builtin_converters.cpp:51:35: error: invalid conversion from ‘const void*’ to ‘void*’ [-fpermissive]  
		       return PyUnicode_Check(obj) ? _PyUnicode_AsString(obj) : 0;  
  
		    "g++"  -ftemplate-depth-128 -O3 -finline-functions -Wno-inline -Wall -pthread -m64  -DBOOST_ALL_NO_LIB=1 -DBOOST_PYTHON_SOURCE -DBOOST_PYTHON_STATIC_LIB -DNDEBUG  -I"." -I"/home/matej/anaconda3/envs/py38/include/python3.8" -c -o "bin.v2/libs/python/build/gcc-7/release/link-static/threading-multi/converter/builtin_converters.o" "libs/python/src/converter/builtin_converters.cpp"  
  
		...failed gcc.compile.c++ bin.v2/libs/python/build/gcc-7/release/link-static/threading-multi/converter/builtin_converters.o...  
		...skipped <pbin.v2/libs/python/build/gcc-7/release/link-static/threading-multi>libboost_python.a(clean) for lack of <pbin.v2/libs/python/build/gcc-7/release/link-static/threading-multi>converter/builtin_converters.o...  
		...skipped <pbin.v2/libs/python/build/gcc-7/release/link-static/threading-multi>libboost_python.a for lack of <pbin.v2/libs/python/build/gcc-7/release/link-static/threading-multi>converter/builtin_converters.o...  
		...skipped <pstage/lib>libboost_python.a for lack of <pbin.v2/libs/python/build/gcc-7/release/link-static/threading-multi>libboost_python.a...  
		...skipped <pbin.v2/libs/python/build/gcc-7/release/link-static/threading-multi>libboost_python3.a(clean) for lack of <pbin.v2/libs/python/build/gcc-7/release/link-static/threading-multi>converter/builtin_converters.o...  
		...skipped <pbin.v2/libs/python/build/gcc-7/release/link-static/threading-multi>libboost_python3.a for lack of <pbin.v2/libs/python/build/gcc-7/release/link-static/threading-multi>converter/builtin_converters.o...  
		...skipped <pstage/lib>libboost_python3.a for lack of <pbin.v2/libs/python/build/gcc-7/release/link-static/threading-multi>libboost_python3.a...  
		gcc.compile.c++ bin.v2/libs/python/build/gcc-7/release/threading-multi/converter/builtin_converters.o  
		libs/python/src/converter/builtin_converters.cpp: In function ‘void* boost::python::converter::{anonymous}::convert_to_cstring(PyObject*)’:  
		libs/python/src/converter/builtin_converters.cpp:51:35: error: invalid conversion from ‘const void*’ to ‘void*’ [-fpermissive]  
		       return PyUnicode_Check(obj) ? _PyUnicode_AsString(obj) : 0;  
  
		    "g++"  -ftemplate-depth-128 -O3 -finline-functions -Wno-inline -Wall -pthread -fPIC -m64  -DBOOST_ALL_NO_LIB=1 -DBOOST_PYTHON_SOURCE -DNDEBUG  -I"." -I"/home/matej/anaconda3/envs/py38/include/python3.8" -c -o "bin.v2/libs/python/build/gcc-7/release/threading-multi/converter/builtin_converters.o" "libs/python/src/converter/builtin_converters.cpp"  
  
		...failed gcc.compile.c++ bin.v2/libs/python/build/gcc-7/release/threading-multi/converter/builtin_converters.o...  
		...skipped <pbin.v2/libs/python/build/gcc-7/release/threading-multi>libboost_python.so.1.63.0 for lack of <pbin.v2/libs/python/build/gcc-7/release/threading-multi>converter/builtin_converters.o...  
		...skipped <pstage/lib>libboost_python.so.1.63.0 for lack of <pbin.v2/libs/python/build/gcc-7/release/threading-multi>libboost_python.so.1.63.0...  
		...skipped <pstage/lib>libboost_python.so for lack of <pstage/lib>libboost_python.so.1.63.0...  
		...skipped <pbin.v2/libs/python/build/gcc-7/release/threading-multi>libboost_numpy.so.1.63.0 for lack of <pbin.v2/libs/python/build/gcc-7/release/threading-multi>libboost_python.so.1.63.0...  
		...skipped <pstage/lib>libboost_numpy.so.1.63.0 for lack of <pbin.v2/libs/python/build/gcc-7/release/threading-multi>libboost_numpy.so.1.63.0...  
		...skipped <pstage/lib>libboost_numpy.so for lack of <pstage/lib>libboost_numpy.so.1.63.0...  
		...skipped <pbin.v2/libs/python/build/gcc-7/release/threading-multi>libboost_python3.so.1.63.0 for lack of <pbin.v2/libs/python/build/gcc-7/release/threading-multi>converter/builtin_converters.o...  
		...skipped <pstage/lib>libboost_python3.so.1.63.0 for lack of <pbin.v2/libs/python/build/gcc-7/release/threading-multi>libboost_python3.so.1.63.0...  
		...skipped <pstage/lib>libboost_python3.so for lack of <pstage/lib>libboost_python3.so.1.63.0...  
		...skipped <pbin.v2/libs/python/build/gcc-7/release/threading-multi>libboost_numpy3.so.1.63.0 for lack of <pbin.v2/libs/python/build/gcc-7/release/threading-multi>libboost_python.so.1.63.0...  
		...skipped <pstage/lib>libboost_numpy3.so.1.63.0 for lack of <pbin.v2/libs/python/build/gcc-7/release/threading-multi>libboost_numpy3.so.1.63.0...  
		...skipped <pstage/lib>libboost_numpy3.so for lack of <pstage/lib>libboost_numpy3.so.1.63.0...  
		...failed updating 2 targets...  
		...skipped 18 targets...

---

## Comment 1

> Username: xhajnal  
> Created at: 2020-12-14 17:23:40 UTC  
> Url: https://github.com/boostorg/python/issues/333#issuecomment-744589166  

probably the same result using gcc and g++ compliers version 9.3.0  
(very very long output - sent upon a request)  
probably the same result using gcc and g++ compliers version 6.5.0  
(very very long output - sent upon a request)  
  
```  
...failed updating 2 targets...  
...skipped 18 targets...  
...updated 1180 targets...  
  
```

---

## Comment 2

> Username: mclow  
> Created at: 2020-12-14 18:05:30 UTC  
> Url: https://github.com/boostorg/python/issues/333#issuecomment-744613141  

Please note that 1.63.0 is a very old boost release (released in 2017) - we just (last week) released 1.75.0.

---

## Comment 3

> Username: kaustubhcs  
> Created at: 2022-06-20 08:29:31 UTC  
> Url: https://github.com/boostorg/python/issues/333#issuecomment-1160136861  

This is very important to highlight, a lot of the boost documentation pages, redirect to the older versions of BOOST.
