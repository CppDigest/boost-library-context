# #474 - Build failure on alpha (duplicate values of SIGPWR, SIGINFO) [Closed]

> Username: thesamesam  
> Created at: 2025-04-07 03:11:54 UTC  
> Updated at: 2025-04-14 19:39:44 UTC  
> Closed at: 2025-04-14 15:51:41 UTC  
> Url: https://github.com/boostorg/process/issues/474  

Originally reported downstream in Gentoo at https://bugs.gentoo.org/942898.  
  
Boost 1.85 built fine, but >=1.86 doesn't:  
```  
gcc.compile.c++ bin.v2/libs/process/build/gcc-14.2/gentoorelease/pch-off/threading-multi/visibility-hidden/error.o  
  
    "alpha-unknown-linux-gnu-g++"   -fvisibility-inlines-hidden  -D_FILE_OFFSET_BITS=64 -D_LARGEFILE_SOURCE -D_LARGEFILE64_SOURCE -mieee -pipe -O2 -mcpu=ev4 -std=c++17 -fPIC -pthread -finline-functions -Wno-inline -Wall -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_PROCESS_DYN_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DNDEBUG   -I"."  -c -o "bin.v2/libs/process/build/gcc-14.2/gentoorelease/pch-off/threading-multi/visibility-hidden/error.o" "libs/process/src/error.cpp"  
  
libs/process/src/error.cpp: In member function ‘virtual std::string boost::process::v2::error::detail::exit_code_category::message(int) const’:  
libs/process/src/error.cpp:128:21: error: duplicate case value  
  128 |                     case SIGPWR: return "SIGPWR:    Power failure (System V)";  
      |                     ^~~~  
libs/process/src/error.cpp:104:21: note: previously used here  
  104 |                     case SIGINFO: return "SIGINFO:   A synonym for SIGPWR";  
      |                     ^~~~  
  
...failed updating 0 target...  
```  
  
On Alpha, `SIGPWR` and `SIGINFO` have the same value.

---

## Comment 1

> Username: thesamesam  
> Created at: 2025-04-14 19:39:43 UTC  
> Url: https://github.com/boostorg/process/issues/474#issuecomment-2802811505  

Thanks!
