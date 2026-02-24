# #73 - compiling boost/iostreams with external lzma links to wrong lzma library [Closed]

> Username: KBentley57  
> Created at: 2018-11-28 21:24:54 UTC  
> Updated at: 2019-05-30 18:32:18 UTC  
> Closed at: 2019-05-30 18:32:18 UTC  
> Url: https://github.com/boostorg/iostreams/issues/73  

I'm trying to build boost 1.68 on linux (CentOS 6) using the developer toolset 7.  Since I'm building zlib, bzip2 and lzma for other parts of the project, I'd like boost to use those during its build.  I am using CMake's ExternalProject_Add with a set of configure, buld, and install steps, with the options below.  All three libraries are in the LIBRARY_PATH and LD_LIBRARY_PATH.  
  
```  
ExternalProject_Add(boost  
	URL  ${CMAKE_CURRENT_SOURCE_DIR}/boost_1_68_0.tar.xz  
	URL_MD5 566027ab95e4d5920d84ab9a7efd4afd  
	PREFIX ${CMAKE_CURRENT_BINARY_DIR}/boost	  
	INSTALL_DIR ${MY_ROOT}  
	UPDATE_COMMAND ""		  
	CONFIGURE_COMMAND   
		./bootstrap.sh   
		--with-python-version=2.7	  
		--with-toolset=gcc   
		--prefix=<INSTALL_DIR>  
		--with-libraries=iostreams,system,thread  
	BUILD_COMMAND   
		./b2   
		--layout=system  
		--build-dir=${CMAKE_CURRENT_BINARY_DIR}/boost/src/boost-build  
		--stage-dir=${CMAKE_CURRENT_BINARY_DIR}/boost/src/boost-stage  
		variant=release  
		link=shared,static  
		address-model=64		  
		threading=multi  
		runtime-link=shared  
		-j4   
		--prefix=<INSTALL_DIR>  
		cxxflags=${CMAKE_CXX_FLAGS_RELEASE}		  
		-sBZIP2_LIBPATH=<INSTALL_DIR>/lib  
		-sBZIP2_BINARY=bz2  
		-sBZIP2_INCLUDE=<INSTALL_DIR>/include		  
		-sLZMA_LIBPATH=<INSTALL_DIR>/lib  
		-sLZMA_BINARY=lzma  
		-sLZMA_INCLUDE=<INSTALL_DIR>/include  
		-sZLIB_LIBPATH=<INSTALL_DIR>/lib  
		-sZLIB_BINARY=z  
		-sZLIB_INCLUDE=<INSTALL_DIR>/include  
		stage  
	INSTALL_COMMAND   
		./b2 install  
	LOG_UPDATE 1  
	LOG_CONFIGURE 1  
	LOG_BUILD 1  
	LOG_INSTALL 1  
	BUILD_IN_SOURCE 1		  
)  
```  
This finishes fine, and I don't see any problems in the build, however, the output below shows that something must have gone wrong.  Any advice?  
```  
$ ldd libboost_iostreams.so  
	linux-vdso.so.1 =>  (0x00007fffe35ef000)  
	librt.so.1 => /lib64/librt.so.1 (0x00007fcd522d0000)  
	libbz2.so.1 => /home/tester/lib/libbz2.so.1 (0x00007fcd520b7000)  
-->	liblzma.so.0 => /usr/lib64/liblzma.so.0 (0x00007fcd51e96000)     
	libz.so.1 => /home/tester/lib/libz.so.1 (0x00007fcd51c7b000)  
	libstdc++.so.6 => /usr/lib64/libstdc++.so.6 (0x00007fcd51974000)  
	libm.so.6 => /lib64/libm.so.6 (0x00007fcd516f0000)  
	libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007fcd514da000)  
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fcd512bc000)  
	libc.so.6 => /lib64/libc.so.6 (0x00007fcd50f28000)  
	/lib64/ld-linux-x86-64.so.2 (0x0000555c26f63000)  
```  
Output from bootstrap  
```  
Building Boost.Build engine with toolset gcc... tools/build/src/engine/bin.linuxx86_64/b2  
Unicode/ICU support for Boost.Regex?... /usr  
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
```  
Output from b2 stage  
```  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - bzip2                    : yes  
    - lzma                     : yes  
    - zlib                     : yes  
    - symlinks supported       : yes  
    - lockfree boost::atomic_flag : yes  
    - bzip2                    : yes  
    - lzma                     : yes  
    - zlib                     : yes  
  
Component configuration:  
  
    - atomic                   : not building  
    - chrono                   : not building  
    - container                : not building  
    - context                  : not building  
    - contract                 : not building  
    - coroutine                : not building  
    - date_time                : not building  
    - exception                : not building  
    - fiber                    : not building  
    - filesystem               : not building  
    - graph                    : not building  
    - graph_parallel           : not building  
    - iostreams                : building  
    - locale                   : not building  
    - log                      : not building  
    - math                     : not building  
    - mpi                      : not building  
    - program_options          : not building  
    - python                   : not building  
    - random                   : not building  
    - regex                    : not building  
    - serialization            : not building  
    - signals                  : not building  
    - stacktrace               : not building  
    - system                   : building  
    - test                     : not building  
    - thread                   : building  
    - timer                    : not building  
    - type_erasure             : not building  
    - wave                     : not building  
...(lots more)  
```  
Output from b2 install  
```  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - bzip2                    : yes  
    - lzma                     : yes  
    - zlib                     : yes  
    - symlinks supported       : yes  
    - lockfree boost::atomic_flag : yes  
    - bzip2                    : yes  
    - lzma                     : yes  
    - zlib                     : yes  
  
Component configuration:  
  
    - atomic                   : not building  
    - chrono                   : not building  
    - container                : not building  
    - context                  : not building  
    - contract                 : not building  
    - coroutine                : not building  
    - date_time                : not building  
    - exception                : not building  
    - fiber                    : not building  
    - filesystem               : not building  
    - graph                    : not building  
    - graph_parallel           : not building  
    - iostreams                : building  
    - locale                   : not building  
    - log                      : not building  
    - math                     : not building  
    - mpi                      : not building  
    - program_options          : not building  
    - python                   : not building  
    - random                   : not building  
    - regex                    : not building  
    - serialization            : not building  
    - signals                  : not building  
    - stacktrace               : not building  
    - system                   : building  
    - test                     : not building  
    - thread                   : building  
    - timer                    : not building  
    - type_erasure             : not building  
    - wave                     : not building  
... (lots more)  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2018-11-29 02:08:41 UTC  
> Url: https://github.com/boostorg/iostreams/issues/73#issuecomment-442679074  

According to boost build sources, you want to define:  
```  
jking@ubuntu:~/boost/tools/build$ grep -r LZMA .  
./src/tools/lzma.jam:# variables LZMA_LIBRARY_PATH, LZMA_NAME, and LZMA_INCLUDE will  
  
jking@ubuntu:~/boost/tools/build$ grep -r BZIP2 .  
./src/tools/bzip2.jam:# variables BZIP2_LIBRARY_PATH, BZIP2_NAME, and BZIP2_INCLUDE will  
  
jking@ubuntu:~/boost/tools/build$ grep -r ZLIB .  
./src/tools/zlib.jam:# variables ZLIB_LIBRARY_PATH, ZLIB_NAME, and ZLIB_INCLUDE will  
```  
  
Give those a try and see if they work.

---

## Comment 2

> Username: swatanabe  
> Created at: 2018-11-29 02:23:41 UTC  
> Url: https://github.com/boostorg/iostreams/issues/73#issuecomment-442682114  

AMDG  
  
On 11/28/2018 07:08 PM, James E. King III wrote:  
> According to boost build sources, you want to define:  
> ```  
> jking@ubuntu:~/boost/tools/build$ grep -r LZMA .  
> ./src/tools/lzma.jam:# variables LZMA_LIBRARY_PATH, LZMA_NAME, and LZMA_INCLUDE will  
> <snip>  
> ```  
>   
> Give those a try and see if they work.  
>   
  
It shouldn't make any difference.  The other  
names are still retained for backwards compatibility.  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: jeking3  
> Created at: 2018-11-29 02:46:18 UTC  
> Url: https://github.com/boostorg/iostreams/issues/73#issuecomment-442686226  

I assume there is a log file one could look at to see the results of the resolution of those libraries?

---

## Comment 4

> Username: KBentley57  
> Created at: 2018-11-29 15:14:43 UTC  
> Url: https://github.com/boostorg/iostreams/issues/73#issuecomment-442869762  

I was under the impression that those names would work fine, so I left them  
in the older style.  I tried it anyway for the sake of completeness, but it  
didn't seem to matter.  There's nothing of value in the config.log file  
that I can see, but it's attached, along with the build output and the  
installation output.  I'm going to follow up with a minimal test case, if  
either of you are interested in trying to replicate the build.  
  
Thanks for the help.  
  
Kyle  
  
On Wed, Nov 28, 2018 at 8:46 PM James E. King III <notifications@github.com>  
wrote:  
  
> I assume there is a log file one could look at to see the results of the  
> resolution of those libraries?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/iostreams/issues/73#issuecomment-442686226>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AQsOCzGPjkjlyEUm4-fvzyjF2-irHNt5ks5uz0p6gaJpZM4Y4d2a>  
> .  
>

---

## Comment 5

> Username: KBentley57  
> Created at: 2018-11-29 16:22:44 UTC  
> Url: https://github.com/boostorg/iostreams/issues/73#issuecomment-442896623  

I thought emailing the logs would attach them here, but I suppose that's how little I know about github!  Anyway, I've created a little repo to aid in the diagnosis.  If you clone it, you should be able to run the test, and reproduce the linking error.  
  
https://github.com/KBentley57/boost-iostreams_test

---

## Comment 6

> Username: KBentley57  
> Created at: 2018-11-29 21:52:43 UTC  
> Url: https://github.com/boostorg/iostreams/issues/73#issuecomment-443007029  

Just another note, I have tried building it without the CMake baggage, using the same inputs to bootstrap and b2.  It turns out the same.

---

## Comment 7

> Username: KBentley57  
> Created at: 2019-05-30 18:32:18 UTC  
> Url: https://github.com/boostorg/iostreams/issues/73#issuecomment-497435916  

Update - This no longer seems to be the behavior as of 1.70.0.  I'm closing this issue, as all are now correctly identified.
