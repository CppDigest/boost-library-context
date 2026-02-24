# #210 - warning: non-free usage requirements <threading>multi ignored [Closed]

> Username: teamblubee  
> Created at: 2018-10-25 01:10:09 UTC  
> Updated at: 2018-10-25 23:50:16 UTC  
> Closed at: 2018-10-25 23:50:16 UTC  
> Url: https://github.com/boostorg/boost/issues/210  

Hello  
  
I am trying to build boost from git clone I run bootstrap.sh with these flags  
```shell  
./bootstrap.sh				\  
--prefix=/usr/local 			        \  
--with-toolset=clang	                \  
--with-icu=/usr/local			\  
--with-python=python27		        \  
	  
```  
After boostrapping I append   
```shell  
	"using mpi ;" >> ./project-config.jam  
```  
  
There's two issues with that  
1)icu isn't found even though icu headers are in /usr/local/unicode/  
2)I get this this warning  
```error  
warning: non-free usage requirements <threading>multi ignored  
warning: in main-target boost_mpi_python at libs/mpi/build/Jamfile.v2:144  
```  
Which I tracked down to this issue: http://boost.2283326.n4.nabble.com/explicitly-specified-values-of-non-free-feature-lt-threading-gt-conflict-td4701191.html  
  
That thread leaves the issue unresolved and just throws a warning instead of failing.  
```shell  
./bjam 						\  
--prefix=/usr/local			        \  
--layout=system				\  
-d4					                \  
--toolset=clang	                        \  
--build-dir=./build		                \  
--stagedir=/opt/boost			\  
-j4			                                \  
include=/usr/local/include/unicode	\  
link=shared				        \  
threading=multi				\  
stage					        \  
```  
  
bjam output  
```shell  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes  
    - C++11 mutex              : yes  
    - lockfree boost::atomic_flag : no  
    - Boost.Config Feature Check: cxx11_auto_declarations : yes  
    - Boost.Config Feature Check: cxx11_constexpr : yes  
    - Boost.Config Feature Check: cxx11_defaulted_functions : yes  
    - Boost.Config Feature Check: cxx11_final : yes  
    - Boost.Config Feature Check: cxx11_hdr_mutex : yes  
    - Boost.Config Feature Check: cxx11_hdr_tuple : yes  
    - Boost.Config Feature Check: cxx11_lambdas : yes  
    - Boost.Config Feature Check: cxx11_noexcept : yes  
    - Boost.Config Feature Check: cxx11_nullptr : yes  
    - Boost.Config Feature Check: cxx11_rvalue_references : yes  
    - Boost.Config Feature Check: cxx11_template_aliases : yes  
    - Boost.Config Feature Check: cxx11_thread_local : yes  
    - Boost.Config Feature Check: cxx11_variadic_templates : yes  
    - has_icu builds           : yes  
warning: non-free usage requirements <threading>multi ignored  
warning: in main-target mpi at project-config.jam:42  
warning: non-free usage requirements <threading>multi ignored  
warning: in main-target boost_mpi at libs/mpi/build/Jamfile.v2:119  
    - zlib                     : yes  
    - bzip2                    : yes  
    - lzma                     : yes  
    - zstd                     : no  
    - iconv (libc)             : yes  
    - icu                      : no  
    - icu (lib64)              : no  
    - xlocale supported        : yes  
    - native-atomic-int32-supported : yes  
    - native-syslog-supported  : yes  
    - pthread-supports-robust-mutexes : yes  
    - compiler-supports-ssse3  : yes  
    - compiler-supports-avx2   : yes  
    - gcc visibility           : yes  
    - long double support      : no  
warning: non-free usage requirements <threading>multi ignored  
warning: in main-target boost_mpi_python at libs/mpi/build/Jamfile.v2:144  
    - libbacktrace builds      : no  
    - addr2line builds         : yes  
    - WinDbg builds            : no  
    - WinDbgCached builds      : no  
    - BOOST_COMP_GNUC >= 4.3.0 : no  
  
Component configuration:  
  
    - atomic                   : building  
    - chrono                   : building  
    - container                : building  
    - context                  : building  
    - contract                 : building  
    - coroutine                : building  
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
    - mpi                      : building  
    - program_options          : building  
    - python                   : building  
    - random                   : building  
    - regex                    : building  
    - serialization            : building  
    - stacktrace               : building  
    - system                   : building  
    - test                     : building  
    - thread                   : building  
    - timer                    : building  
    - type_erasure             : building  
    - wave                     : building  
```  
  
I do have a working installation of openmpi installed in /usr/local and mpicxx is in my path from following the directions here: https://www.boost.org/doc/libs/1_64_0/doc/html/mpi/getting_started.html#mpi.config  
  
  
in the end the build errors out  
```shell  
0.003049 sec system; 0.000000 sec user; 3.078255 sec clock  
Using shell: /bin/sh -c  
    argv[0] = '/bin/sh'  
    argv[1] = '-c'  
    argv[2] = '  
    ln -f -s 'libboost_wave.so.1.69.0' '/wrkdirs/usr/ports/devel/boost-devel/work/stage/lib/libboost_wave.so'  
'  
ln-UNIX /wrkdirs/usr/ports/devel/boost-devel/work/stage/lib/libboost_wave.so  
  
    ln -f -s 'libboost_wave.so.1.69.0' '/wrkdirs/usr/ports/devel/boost-devel/work/stage/lib/libboost_wave.so'  
  
0.001905 sec system; 0.000000 sec user; 1.957884 sec clock  
...failed updating 86 targets...  
...skipped 17 targets...  
...updated 1147 targets...  
*** [do-build] Error code 1  
  
make: stopped in /usr/ports/devel/boost-devel  
1 error  
```  
  
two questions:  
1) How do I solve the non-free usage requirements warning  
2) Why isn't icu being picked up?
