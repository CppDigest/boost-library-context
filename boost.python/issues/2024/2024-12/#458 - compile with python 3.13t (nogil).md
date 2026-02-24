# #458 - compile with python 3.13t (nogil) [Open]

> Username: subaochen  
> Created at: 2024-12-02 03:38:59 UTC  
> Updated at: 2026-02-09 22:00:04 UTC  
> Url: https://github.com/boostorg/python/issues/458  

I have installed free-threading python 3.13 following https://py-free-threading.github.io/installing_cpython/ on ubuntu 22.04(with gcc 11.04.0),  with command:  
  
`conda create -n nogil --override-channels -c conda-forge python-freethreading`  
  
But compile python 3.13t  with boost-1.86.0 failed, below the steps:  
  
1, configure boost successfully:  
`./bootstrap.sh --with-python=/home/mydesktop/anaconda3/envs/nogil`  
  
2, compile boost failed:  
`./b2 --toolset=gcc --with-python include="/home/mydesktop/anaconda3/envs/nogil/include/python3.13t/" --with-thread --with-date_time --with-chrono --with-system`  
3, error message:  
```  
`gcc.compile.c++ bin.v2/libs/python/build/gcc-11/release/x86_64/link-static/python-3.13/threading-multi/visibility-hidden/dict.o  
In file included from ./boost/python/detail/prefix.hpp:13,  
                 from ./boost/python/dict.hpp:8,  
                 from libs/python/src/dict.cpp:4:  
./boost/python/detail/wrap_python.hpp:57:11: fatal error: pyconfig.h: No such file or directory  
   57 | # include <pyconfig.h>  
      |           ^~~~~~~~~~~~  
compilation terminated.  
  
    "g++"   -fvisibility-inlines-hidden -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_PYTHON_SOURCE -DBOOST_PYTHON_STATIC_LIB -DNDEBUG   -I"." -I"/home/subaochen/anaconda3/envs/nogil/include/python3.13" -I"/home/subaochen/anaconda3/envs/nogil/include/python3.1t"  -c -o "bin.v2/libs/python/build/gcc-11/release/x86_64/link-static/python-3.13/threading-multi/visibility-hidden/dict.o" "libs/python/src/dict.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/python/build/gcc-11/release/x86_64/link-static/python-3.13/threading-multi/visibility-hidden/dict.o...  
gcc.compile.c++ bin.v2/libs/python/build/gcc-11/release/x86_64/link-static/python-3.13/threading-multi/visibility-hidden/str.o  
In file included from ./boost/python/detail/prefix.hpp:13,  
                 from ./boost/python/str.hpp:8,  
                 from libs/python/src/str.cpp:4:  
./boost/python/detail/wrap_python.hpp:57:11: fatal error: pyconfig.h: No such file or directory  
   57 | # include <pyconfig.h>  
      |           ^~~~~~~~~~~~  
compilation terminated.  
```  
  
  
Obviously I missed "pyconfig.h", but i successfully compiled boost 1.86 with python 3.11 with the same step. Any suggestions? Thanks in advance!

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2024-12-02 15:00:46 UTC  
> Url: https://github.com/boostorg/python/issues/458#issuecomment-2511775419  

@grafikrobot , do you know what's wrong here ? My b2 knowledge is next to non-existent.

---

## Comment 2

> Username: grafikrobot  
> Created at: 2024-12-07 17:27:08 UTC  
> Url: https://github.com/boostorg/python/issues/458#issuecomment-2525253083  

@stefanseefeld hmm.. something strange is going on with the include paths for python:  
  
```  
-I"/home/subaochen/anaconda3/envs/nogil/include/python3.13"  
-I"/home/subaochen/anaconda3/envs/nogil/include/python3.1t"  
```  
  
It would help if we got the output of running the build with the `--debug-configuration` option.

---

## Comment 3

> Username: rgommers  
> Created at: 2025-04-16 10:27:53 UTC  
> Url: https://github.com/boostorg/python/issues/458#issuecomment-2809143217  

I can reproduce this build error if I leave out the `include="/path/to/include/python3.13t/"` part, if I add it things do work. Full reproducer:  
  
```  
$ git clone git@github.com:boostorg/boost.git  
$ git submodule update --init --recursive  
$ cd boost  
  
$ conda create -n boostpython python-freethreading  compilers  
$ conda activate boostpython  
$ ./bootstrap.sh --with-python=$(which python)  
$ ./b2 --toolset=gcc --with-python --with-thread --with-date_time --with-chrono --with-system  
...  
    "g++"   -fvisibility-inlines-hidden -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_CONTAINER_NO_LIB=1 -DBOOST_CONTAINER_STATIC_LINK=1 -DBOOST_PYTHON_SOURCE -DBOOST_PYTHON_STATIC_LIB -DNDEBUG   -I"." -I"/home/rgommers/code/tmp/boost/libs/any/include" -I"/home/rgommers/code/tmp/boost/libs/assert/include" -I"/home/rgommers/code/tmp/boost/libs/bind/include" -I"/home/rgommers/code/tmp/boost/libs/concept_check/include" -I"/home/rgommers/code/tmp/boost/libs/container/include" -I"/home/rgommers/code/tmp/boost/libs/container_hash/include" -I"/home/rgommers/code/tmp/boost/libs/conversion/include" -I"/home/rgommers/code/tmp/boost/libs/core/include" -I"/home/rgommers/code/tmp/boost/libs/describe/include" -I"/home/rgommers/code/tmp/boost/libs/detail/include" -I"/home/rgommers/code/tmp/boost/libs/function/include" -I"/home/rgommers/code/tmp/boost/libs/integer/include" -I"/home/rgommers/code/tmp/boost/libs/intrusive/include" -I"/home/rgommers/code/tmp/boost/libs/iterator/include" -I"/home/rgommers/code/tmp/boost/libs/lexical_cast/include" -I"/home/rgommers/code/tmp/boost/libs/move/include" -I"/home/rgommers/code/tmp/boost/libs/mp11/include" -I"/home/rgommers/code/tmp/boost/libs/mpl/include" -I"/home/rgommers/code/tmp/boost/libs/optional/include" -I"/home/rgommers/code/tmp/boost/libs/predef/include" -I"/home/rgommers/code/tmp/boost/libs/property_map/include" -I"/home/rgommers/code/tmp/boost/libs/python/include" -I"/home/rgommers/code/tmp/boost/libs/smart_ptr/include" -I"/home/rgommers/code/tmp/boost/libs/throw_exception/include" -I"/home/rgommers/code/tmp/boost/libs/tuple/include" -I"/home/rgommers/code/tmp/boost/libs/type_index/include" -I"/home/rgommers/code/tmp/boost/libs/type_traits/include" -I"/home/rgommers/code/tmp/boost/libs/utility/include" -I"/home/rgommers/mambaforge/envs/boostpython/include/python3.13" -I"libs/config/include"  -c -o "bin.v2/libs/python/build/gcc-13/release/x86_64/link-static/python-3.13/threading-multi/visibility-hidden/exec.o" "/home/rgommers/code/tmp/boost/libs/python/src/exec.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/python/build/gcc-13/release/x86_64/link-static/python-3.13/threading-multi/visibility-hidden/exec.o...  
gcc.compile.c++ bin.v2/libs/python/build/gcc-13/release/x86_64/link-static/python-3.13/threading-multi/visibility-hidden/object/function_doc_signature.o  
In file included from ./boost/python/detail/prefix.hpp:13,  
                 from ./boost/python/converter/registrations.hpp:8,  
                 from /home/rgommers/code/tmp/boost/libs/python/src/object/function_doc_signature.cpp:9:  
./boost/python/detail/wrap_python.hpp:57:11: fatal error: pyconfig.h: No such file or directory  
   57 | # include <pyconfig.h>  
      |           ^~~~~~~~~~~~  
compilation terminated.  
```  
  
Change the build command to add an explicit include path:  
```  
$ ./b2 --toolset=gcc --with-python include=/home/rgommers/mambaforge/envs/boostpython/include/python3.13t --with-thread --with-date_time --with-chrono --with-system  
...  
The Boost C++ Libraries were successfully built!  
```  
  
The generated `project-config.jam` file does set the path to the root dir for the Python executable, but no related include path:  
  
<details>  
  
```  
# B2 Configuration  
# Automatically generated by bootstrap.sh  
  
import option ;  
import feature ;  
  
# Compiler configuration. This definition will be used unless  
# you already have defined some toolsets in your user-config.jam  
# file.  
if ! gcc in [ feature.values <toolset> ]  
{  
    using gcc ;   
}  
  
project : default-build <toolset>gcc ;  
  
# Python configuration  
import python ;  
if ! [ python.configured ]  
{  
    using python : 3.13 : "/home/rgommers/mambaforge/envs/boostpython" ;  
}  
  
path-constant ICU_PATH : /usr ;  
  
  
# List of --with-<library> and --without-<library>  
# options. If left empty, all libraries will be built.  
# Options specified on the command line completely  
# override this variable.  
libraries =  ;  
  
# These settings are equivalent to corresponding command-line  
# options.  
option.set prefix : /usr/local ;  
option.set exec-prefix : /usr/local ;  
option.set libdir : /usr/local/lib ;  
option.set includedir : /usr/local/include ;  
  
# Stop on first error  
option.set keep-going : false ;  
```  
  
</details>  
  
Knowing where that include path is set would be helpful I guess (I'm not familiar with `b2` either) - or is it expected that one has to manually add it if the `python` executable isn't the system one?  
  
> It would help if we got the output of running the build with the `--debug-configuration` option.  
  
<details>  
<summary>Output up the the first build failure</summary>  
  
```  
notice: found boost-build.jam at /home/rgommers/code/tmp/boost/boost-build.jam  
notice: loading B2 from /home/rgommers/code/tmp/boost/tools/build/src/build-system.jam  
notice: Searching '/etc' '/home/rgommers' '/home/rgommers/code/tmp/boost/tools/build/src/util' '/home/rgommers/code/tmp/boost/tools/build/src/tools' '/home/rgommers/code/tmp/boost/tools/build/src/options' '/home/rgommers/code/tmp/boost/tools/build/src/engine' '/home/rgommers/code/tmp/boost/tools/build/src/contrib' '/home/rgommers/code/tmp/boost/tools/build/src/build' '/home/rgommers/code/tmp/boost/tools/build/src' for site-config configuration file 'site-config.jam'.  
notice: Configuration file 'site-config.jam' not found in '/etc' '/home/rgommers' '/home/rgommers/code/tmp/boost/tools/build/src/util' '/home/rgommers/code/tmp/boost/tools/build/src/tools' '/home/rgommers/code/tmp/boost/tools/build/src/options' '/home/rgommers/code/tmp/boost/tools/build/src/engine' '/home/rgommers/code/tmp/boost/tools/build/src/contrib' '/home/rgommers/code/tmp/boost/tools/build/src/build' '/home/rgommers/code/tmp/boost/tools/build/src'.  
notice: Searching '/home/rgommers' '/home/rgommers/code/tmp/boost/tools/build/src/util' '/home/rgommers/code/tmp/boost/tools/build/src/tools' '/home/rgommers/code/tmp/boost/tools/build/src/options' '/home/rgommers/code/tmp/boost/tools/build/src/engine' '/home/rgommers/code/tmp/boost/tools/build/src/contrib' '/home/rgommers/code/tmp/boost/tools/build/src/build' '/home/rgommers/code/tmp/boost/tools/build/src' for user-config configuration file 'user-config.jam'.  
notice: Configuration file 'user-config.jam' not found in '/home/rgommers' '/home/rgommers/code/tmp/boost/tools/build/src/util' '/home/rgommers/code/tmp/boost/tools/build/src/tools' '/home/rgommers/code/tmp/boost/tools/build/src/options' '/home/rgommers/code/tmp/boost/tools/build/src/engine' '/home/rgommers/code/tmp/boost/tools/build/src/contrib' '/home/rgommers/code/tmp/boost/tools/build/src/build' '/home/rgommers/code/tmp/boost/tools/build/src'.  
notice: Searching '.' for project-config configuration file 'project-config.jam'.  
notice: Loading project-config configuration file 'project-config.jam' from '.'.  
notice: will use 'g++' for gcc, condition <toolset>gcc-13  
notice: using gcc libraries :: <toolset>gcc-13 :: /home/rgommers/mambaforge/envs/boostpython/bin /home/rgommers/mambaforge/envs/boostpython/lib /home/rgommers/mambaforge/envs/boostpython/lib32 /home/rgommers/mambaforge/envs/boostpython/lib64  
notice: using gcc archiver :: <toolset>gcc-13 :: /home/rgommers/mambaforge/envs/boostpython/x86_64-conda-linux-gnu/bin/ar  
warning: toolset gcc initialization: can not find tool windres  
warning: initialized from project-config.jam:12  
notice: using rc compiler :: <toolset>gcc-13 :: /home/rgommers/mambaforge/envs/boostpython/bin/as  
notice: [python-cfg] Configuring python...  
notice: [python-cfg]   user-specified version: "3.13"  
notice: [python-cfg]   user-specified cmd-or-prefix: "/home/rgommers/mambaforge/envs/boostpython"  
notice: [python-cfg] Checking interpreter command "/home/rgommers/mambaforge/envs/boostpython/bin/python3.13"...  
notice: [python-cfg] running command '/home/rgommers/mambaforge/envs/boostpython/bin/python3.13 -c "from sys import *; print('version=%d.%d\nplatform=%s\nprefix=%s\nexec_prefix=%s\nexecutable=%s' % (version_info[0],version_info[1],platform,prefix,exec_prefix,executable))" 2>&1'  
notice: [python-cfg] ...requested configuration matched!  
notice: [python-cfg] Details of this Python configuration:  
notice: [python-cfg]   interpreter command: "/home/rgommers/mambaforge/envs/boostpython/bin/python3.13"  
notice: [python-cfg]   include path: "/home/rgommers/mambaforge/envs/boostpython/include/python3.13"  
notice: [python-cfg]   library path: "/home/rgommers/mambaforge/envs/boostpython/lib/python3.13/config" "/home/rgommers/mambaforge/envs/boostpython/lib"  
notice: [python-cfg] Checking for NumPy...  
notice: [python-cfg] running command '/home/rgommers/mambaforge/envs/boostpython/bin/python3.13 -c "import sys; sys.stderr = sys.stdout; import numpy; print(numpy.get_include())"'  
notice: [python-cfg] NumPy disabled. Reason:  
notice: [python-cfg]   /home/rgommers/mambaforge/envs/boostpython/bin/python3.13 -c "import sys; sys.stderr = sys.stdout; import numpy; print(numpy.get_include())" aborted with   
notice: [python-cfg]   Traceback (most recent call last):  
  File "<string>", line 1, in <module>  
    import sys; sys.stderr = sys.stdout; import numpy; print(numpy.get_include())  
                                         ^^^^^^^^^^^^  
ModuleNotFoundError: No module named 'numpy'  
notice: [cmdline-cfg] adding toolset=gcc to the build request.  
Performing configuration checks  
  
    - default address-model    : 64-bit [1]  
    - default architecture     : x86 [1]  
  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : yes  
    - lockfree boost::atomic_flag : no [2]  
    - compiler supports SSE2   : yes [2]  
    - compiler supports SSE4.1 : yes [2]  
    - cxx11_static_assert      : yes [2]  
    - cxx11_hdr_ratio          : yes [2]  
    - cxx11_template_aliases   : yes [2]  
    - cxx11_decltype           : yes [2]  
    - cxx11_char16_t           : yes [2]  
    - cxx11_char32_t           : yes [2]  
    - lockfree boost::atomic_flag : no [3]  
    - compiler supports SSE2   : yes [3]  
    - compiler supports SSE4.1 : yes [3]  
    - cxx11_static_assert      : yes [3]  
    - cxx11_hdr_ratio          : yes [3]  
    - cxx11_template_aliases   : yes [3]  
    - cxx11_decltype           : yes [3]  
    - cxx11_char16_t           : yes [3]  
    - cxx11_char32_t           : yes [3]  
  
[1] gcc-13  
[2] gcc-13/release/x86_64/python-3.13/threading-multi/visibility-hidden  
[3] gcc-13/release/x86_64/link-static/python-3.13/threading-multi/visibility-hidden  
  
Component configuration:  
  
    - contract                 : not building  
    - date_time                : building  
    - exception                : not building  
    - graph                    : not building  
    - graph_parallel           : not building  
    - headers                  : not building  
    - log                      : not building  
    - nowide                   : not building  
    - program_options          : not building  
    - regex                    : not building  
    - serialization            : not building  
    - test                     : not building  
    - thread                   : building  
    - type_erasure             : not building  
    - yap                      : not building  
    - winapi                   : not building  
    - wave                     : not building  
    - variant2                 : not building  
    - variant                  : not building  
    - uuid                     : not building  
    - utility                  : not building  
    - url                      : not building  
    - unordered                : not building  
    - type_traits              : not building  
    - type_index               : not building  
    - tuple                    : not building  
    - tokenizer                : not building  
    - timer                    : not building  
    - throw_exception          : not building  
    - system                   : building  
    - stl_interfaces           : not building  
    - stacktrace               : not building  
    - spirit                   : not building  
    - sort                     : not building  
    - smart_ptr                : not building  
    - signals2                 : not building  
    - scope                    : not building  
    - redis                    : not building  
    - rational                 : not building  
    - ratio                    : not building  
    - random                   : not building  
    - python                   : building  
    - ptr_container            : not building  
    - property_map             : not building  
    - process                  : not building  
    - predef                   : not building  
    - pool                     : not building  
    - poly_collection          : not building  
    - pfr                      : not building  
    - parameter                : not building  
    - outcome                  : not building  
    - optional                 : not building  
    - mysql                    : not building  
    - multi_index              : not building  
    - msm                      : not building  
    - mqtt5                    : not building  
    - mpl                      : not building  
    - mpi                      : not building  
    - mp11                     : not building  
    - move                     : not building  
    - metaparse                : not building  
    - math                     : not building  
    - logic                    : not building  
    - lockfree                 : not building  
    - locale                   : not building  
    - lexical_cast             : not building  
    - lambda2                  : not building  
    - json                     : not building  
    - iterator                 : not building  
    - iostreams                : not building  
    - intrusive                : not building  
    - interprocess             : not building  
    - integer                  : not building  
    - heap                     : not building  
    - hana                     : not building  
    - geometry                 : not building  
    - function_types           : not building  
    - function                 : not building  
    - format                   : not building  
    - flyweight                : not building  
    - filesystem               : not building  
    - fiber                    : not building  
    - endian                   : not building  
    - dynamic_bitset           : not building  
    - dll                      : not building  
    - detail                   : not building  
    - describe                 : not building  
    - crc                      : not building  
    - coroutine2               : not building  
    - coroutine                : not building  
    - core                     : not building  
    - conversion               : not building  
    - context                  : not building  
    - container_hash           : not building  
    - container                : not building  
    - config                   : not building  
    - concept_check            : not building  
    - compat                   : not building  
    - cobalt                   : not building  
    - chrono                   : building  
    - charconv                 : not building  
    - bind                     : not building  
    - bimap                    : not building  
    - beast                    : not building  
    - atomic                   : not building  
    - assign                   : not building  
    - assert                   : not building  
    - asio                     : not building  
    - array                    : not building  
    - any                      : not building  
  
...patience...  
...found 2320 targets...  
...updating 1027 targets...  
link.mklink boost/assert.hpp  
link.mklink boost/ref.hpp  
mklink-or-dir boost/function  
mklink-or-dir boost/property_map  
mklink-or-dir boost/graph  
mklink-or-dir boost/detail  
link.mklink boost/throw_exception.hpp  
mklink-or-dir boost/exception  
mklink-or-dir boost/assert  
mklink-or-dir boost/algorithm  
link.mklink boost/regex.hpp  
link.mklink boost/function.hpp  
link.mklink boost/foreach.hpp  
link.mklink boost/optional.hpp  
mklink-or-dir boost/property_tree  
mklink-or-dir boost/container  
mklink-or-dir boost/intrusive  
mklink-or-dir boost/python  
mklink-or-dir boost/mpl  
link.mklink boost/cstdint.hpp  
mklink-or-dir boost/bind  
link.mklink boost/integer_traits.hpp  
link.mklink boost/get_pointer.hpp  
link.mklink boost/is_placeholder.hpp  
link.mklink boost/detail/workaround.hpp  
link.mklink boost/detail/binary_search.hpp  
link.mklink boost/graph/graph_traits.hpp  
link.mklink boost/graph/dll_import_export.hpp  
link.mklink boost/graph/graphviz.hpp  
link.mklink boost/graph/graphml.hpp  
link.mklink boost/visit_each.hpp  
link.mklink boost/graph/breadth_first_search.hpp  
link.mklink boost/type.hpp  
mklink-or-dir boost/core  
link.mklink boost/limits.hpp  
mklink-or-dir boost/preprocessor  
link.mklink boost/static_assert.hpp  
mklink-or-dir boost/type_traits  
link.mklink boost/shared_ptr.hpp  
link.mklink boost/noncopyable.hpp  
link.mklink boost/version.hpp  
link.mklink boost/detail/indirect_traits.hpp  
link.mklink boost/detail/select_type.hpp  
mklink-or-dir boost/smart_ptr  
mklink-or-dir boost/pending  
link.mklink boost/graph/graph_concepts.hpp  
link.mklink boost/graph/visitors.hpp  
link.mklink boost/graph/named_function_params.hpp  
link.mklink boost/graph/overloading.hpp  
link.mklink boost/graph/two_bit_color_map.hpp  
mklink-or-dir boost/graph/detail  
mklink-or-dir boost/concept  
link.mklink boost/lexical_cast.hpp  
link.mklink boost/any.hpp  
link.mklink boost/graph/exception.hpp  
mklink-or-dir boost/utility  
link.mklink boost/detail/is_xxx.hpp  
link.mklink boost/call_traits.hpp  
mklink-or-dir boost/iterator  
mklink-or-dir boost/align  
link.mklink boost/detail/call_traits.hpp  
link.mklink boost/utility/value_init.hpp  
mklink-or-dir boost/any  
link.mklink boost/type_index.hpp  
mklink-or-dir boost/lexical_cast  
link.mklink boost/shared_array.hpp  
link.mklink boost/utility/enable_if.hpp  
link.mklink boost/utility/result_of.hpp  
link.mklink boost/preprocessor.hpp  
mklink-or-dir boost/parameter  
link.mklink boost/type_traits.hpp  
link.mklink boost/graph/numeric_values.hpp  
link.mklink boost/graph/buffer_concepts.hpp  
link.mklink boost/concept_check.hpp  
mklink-or-dir boost/tuple  
link.mklink boost/graph/properties.hpp  
link.mklink boost/graph/subgraph.hpp  
link.mklink boost/graph/compressed_sparse_row_graph.hpp  
link.mklink boost/graph/iteration_macros.hpp  
link.mklink boost/pending/queue.hpp  
mklink-or-dir boost/spirit  
...on 100th target...  
link.mklink boost/iterator_adaptors.hpp  
link.mklink boost/compressed_pair.hpp  
link.mklink boost/scoped_ptr.hpp  
mklink-or-dir boost/move  
link.mklink boost/weak_ptr.hpp  
mklink-or-dir boost/thread  
link.mklink boost/aligned_storage.hpp  
link.mklink boost/graph/filtered_graph.hpp  
link.mklink boost/detail/numeric_traits.hpp  
mklink-or-dir boost/mp11  
link.mklink boost/integer.hpp  
mklink-or-dir boost/fusion  
link.mklink boost/graph/graph_selectors.hpp  
mklink-or-dir boost/functional  
link.mklink boost/next_prior.hpp  
link.mklink boost/graph/graph_mutability_traits.hpp  
link.mklink boost/indirect_reference.hpp  
link.mklink boost/current_function.hpp  
mklink-or-dir boost/xpressive  
mklink-or-dir boost/range  
...patience...  
mklink-or-dir boost/proto  
link.mklink boost/intrusive_ptr.hpp  
mklink-or-dir boost/numeric  
link.mklink boost/checked_delete.hpp  
link.mklink boost/utility.hpp  
link.mklink boost/implicit_cast.hpp  
link.mklink boost/detail/is_incrementable.hpp  
link.mklink boost/functional/hash.hpp  
link.mklink boost/integer_fwd.hpp  
link.mklink boost/graph/adjacency_iterator.hpp  
link.mklink boost/unordered_set.hpp  
mklink-or-dir boost/system  
mklink-or-dir boost/date_time  
mklink-or-dir boost/chrono  
mklink-or-dir boost/winapi  
mklink-or-dir boost/predef  
link.mklink boost/pointee.hpp  
link.mklink boost/detail/interlocked.hpp  
link.mklink boost/pending/property.hpp  
mklink-or-dir boost/graph/property_maps  
mklink-or-dir boost/typeof  
link.mklink boost/atomic.hpp  
link.mklink boost/detail/compressed_pair.hpp  
...patience...  
link.mklink boost/memory_order.hpp  
mklink-or-dir boost/atomic  
mklink-or-dir boost/pending/detail  
link.mklink boost/predef.h  
mklink-or-dir boost/ratio  
link.mklink boost/operators.hpp  
mklink-or-dir boost/unordered  
mklink-or-dir boost/container_hash  
link.mklink boost/utility/base_from_member.hpp  
link.mklink boost/utility/binary.hpp  
link.mklink boost/utility/identity_type.hpp  
mklink-or-dir boost/numeric/conversion  
link.mklink boost/utility/addressof.hpp  
link.mklink boost/parameter/name.hpp  
link.mklink boost/parameter/binding.hpp  
mklink-or-dir boost/utility/detail  
link.mklink boost/detail/lcast_precision.hpp  
link.mklink boost/detail/basic_pointerbuf.hpp  
mklink-or-dir boost/type_index  
link.mklink boost/mem_fn.hpp  
link.mklink boost/function_equal.hpp  
link.mklink boost/parameter/is_argument_pack.hpp  
mklink-or-dir boost/parameter/aux_  
link.mklink boost/parameter/config.hpp  
link.mklink boost/parameter/keyword.hpp  
link.mklink boost/parameter/template_keyword.hpp  
mklink-or-dir boost/describe  
link.mklink boost/operators_v1.hpp  
link.mklink boost/pending/detail/property.hpp  
mklink-or-dir boost/optional  
link.mklink boost/multi_index_container.hpp  
mklink-or-dir boost/multi_index  
link.mklink boost/foreach_fwd.hpp  
mklink-or-dir boost/regex  
link.mklink boost/scoped_array.hpp  
link.mklink boost/regex_fwd.hpp  
link.mklink boost/multi_index_container_fwd.hpp  
link.mklink boost/none.hpp  
link.mklink boost/exception/exception.hpp  
link.mklink boost/exception/info.hpp  
link.mklink boost/graph/reverse_graph.hpp  
link.mklink boost/graph/adjacency_list.hpp  
link.mklink boost/property_map/dynamic_property_map.hpp  
link.mklink boost/property_map/property_map.hpp  
link.mklink boost/property_map/shared_array_property_map.hpp  
link.mklink boost/property_map/transform_value_property_map.hpp  
...on 200th target...  
link.mklink boost/concept_archetype.hpp  
link.mklink boost/property_map/vector_property_map.hpp  
link.mklink boost/smart_ptr.hpp  
link.mklink boost/graph/named_graph.hpp  
link.mklink boost/parameter/keyword_fwd.hpp  
link.mklink boost/parameter/aux_/is_tagged_argument.hpp  
link.mklink boost/parameter/aux_/arg_list.hpp  
link.mklink boost/parameter/aux_/void.hpp  
link.mklink boost/parameter/aux_/is_placeholder.hpp  
link.mklink boost/parameter/aux_/result_of0.hpp  
link.mklink boost/parameter/aux_/name.hpp  
link.mklink boost/pending/container_traits.hpp  
link.mklink boost/exception/to_string_stub.hpp  
mklink-or-dir boost/exception/detail  
link.mklink boost/none_t.hpp  
mklink-or-dir boost/parameter/aux_/preprocessor  
link.mklink boost/exception/to_string.hpp  
link.mklink boost/unordered_map.hpp  
link.mklink boost/parameter/value_type.hpp  
link.mklink boost/parameter/aux_/lambda_tag.hpp  
link.mklink boost/parameter/aux_/use_default_tag.hpp  
link.mklink boost/parameter/aux_/yesno.hpp  
link.mklink boost/parameter/aux_/is_maybe.hpp  
link.mklink boost/parameter/aux_/tagged_argument_fwd.hpp  
link.mklink boost/parameter/aux_/parameter_requirements.hpp  
link.mklink boost/parameter/aux_/augment_predicate.hpp  
link.mklink boost/parameter/aux_/template_keyword.hpp  
link.mklink boost/parameter/aux_/tag.hpp  
link.mklink boost/parameter/aux_/default.hpp  
link.mklink boost/cast.hpp  
...patience...  
link.mklink boost/polymorphic_cast.hpp  
link.mklink boost/parameter/aux_/unwrap_cv_reference.hpp  
link.mklink boost/parameter/aux_/tagged_argument.hpp  
link.mklink boost/parameter/aux_/has_nested_template_fn.hpp  
mklink-or-dir boost/parameter/aux_/pack  
mklink-or-dir boost/io  
link.mklink boost/enable_shared_from_this.hpp  
link.mklink boost/make_shared.hpp  
link.mklink boost/exception_ptr.hpp  
link.mklink boost/exception/diagnostic_information.hpp  
common.copy /home/rgommers/code/tmp/boost/stage/lib/cmake/BoostDetectToolset-1.89.0.cmake  
common.copy /home/rgommers/code/tmp/boost/stage/lib/cmake/Boost-1.89.0/BoostConfig.cmake  
link.mklink boost/exception/get_error_info.hpp  
link.mklink boost/exception/current_exception_cast.hpp  
link.mklink boost/io_fwd.hpp  
...on 300th target...  
common.copy /home/rgommers/code/tmp/boost/stage/lib/cmake/Boost-1.89.0/BoostConfigVersion.cmake  
common.copy /home/rgommers/code/tmp/boost/stage/lib/cmake/boost_headers-1.89.0/boost_headers-config-version.cmake  
common.copy /home/rgommers/code/tmp/boost/stage/lib/cmake/boost_headers-1.89.0/boost_headers-config.cmake  
common.copy /home/rgommers/code/tmp/boost/stage/lib/cmake/boost_numpy-1.89.0/boost_numpy-config-version.cmake  
common.copy /home/rgommers/code/tmp/boost/stage/lib/cmake/boost_numpy-1.89.0/boost_numpy-config.cmake  
gcc.compile.c++ bin.v2/libs/python/build/gcc-13/release/x86_64/python-3.13/threading-multi/visibility-hidden/list.o  
In file included from ./boost/python/detail/prefix.hpp:13,  
                 from ./boost/python/list.hpp:8,  
                 from /home/rgommers/code/tmp/boost/libs/python/src/list.cpp:5:  
./boost/python/detail/wrap_python.hpp:57:11: fatal error: pyconfig.h: No such file or directory  
   57 | # include <pyconfig.h>  
      |           ^~~~~~~~~~~~  
compilation terminated.  
```  
  
</details>  
  
I checked that my CMake is recent enough; it's at 3.31.6 while 3.30.0 is the minimum version needed for free-threaded CPython support (such versions are tracked [here](https://py-free-threading.github.io/tracking/), I'll add Boost.Python to that table as well).  
  
In the end this looks like all it needs is appending a single `t` to the include path that the build system calculates based on the Python minor version.

---

## Comment 4

> Username: rgommers  
> Created at: 2025-04-16 10:42:43 UTC  
> Updated at: 2025-04-19 19:55:22 UTC  
> Url: https://github.com/boostorg/python/issues/458#issuecomment-2809181009  

Okay, found it I think:  
  
https://github.com/bfgroup/b2/blob/f518c83dbbcce621df81ab29e7b2a33498a35ca2/src/tools/python.jam#L563  
  
`version` is still `3.13` for a free-threaded interpreter, so what is needed is to detect that a free-threaded interpreter is being run and if so, append `t`. The solution will be similar to https://github.com/mesonbuild/meson/pull/13338 I think: run the interpreter, and check for this `sysconfig` variable:  
```python  
>>> import sysconfig  
>>> sysconfig.get_config_var('Py_GIL_DISABLED')  
1  
```

---

## Comment 5

> Username: rgommers  
> Created at: 2026-02-09 22:00:04 UTC  
> Url: https://github.com/boostorg/python/issues/458#issuecomment-3874095024  

This is all fixed after gh-504, the issue can be closed.
