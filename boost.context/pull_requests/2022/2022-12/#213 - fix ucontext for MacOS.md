# #213 fix ucontext for MacOS [Merged]

> Username: eduardvoronkin  
> Created at: 2022-12-16 01:40:13 UTC  
> Updated at: 2022-12-17 10:38:53 UTC  
> Merged at: 2022-12-17 10:38:48 UTC  
> Closed at: 2022-12-17 10:38:48 UTC  
> Url: https://github.com/boostorg/context/pull/213  

1. MacOS version of ::makecontext (tested on m1 Mac Ventura 13.0.1) is able to pass only 4-byte parameters to context function  
2. define _XOPEN_SOURCE before inclusion of predef.h  
  
https://github.com/boostorg/context/issues/184

---

## Comment 1

> Username: elfprince13  
> Created_at: 2022-12-16 03:06:41 UTC  
> Url: https://github.com/boostorg/context/pull/213#issuecomment-1354124643  

So sounds like this likely _doesn't_ solve #198?

---

## Comment 2

> Username: eduardvoronkin  
> Created_at: 2022-12-16 05:19:29 UTC  
> Updated_at: 2022-12-16 05:25:59 UTC  
> Url: https://github.com/boostorg/context/pull/213#issuecomment-1354231915  

> So sounds like this likely _doesn't_ solve #198?  
  
This should solve this. At least, we got boost asio/context working with address sanitizer. Another tricky thing is that you need to keep track of is the _XOPEN_SOURCE macro. I fixed this issue in context, but the same issue remains in boost.asio and actually this is MacOS issue. There are some "bad" headers that lead to inclusion of /sys/_types/_ucontext.h (the file with definition of ucontext struct) WITHOUT this macro. So, as you see from definition   
```cpp  
_STRUCT_UCONTEXT  
{  
	int                     uc_onstack;  
	__darwin_sigset_t       uc_sigmask;     /* signal mask used by this context */  
	_STRUCT_SIGALTSTACK     uc_stack;       /* stack used by this context */  
	_STRUCT_UCONTEXT        *uc_link;       /* pointer to resuming context */  
	__darwin_size_t         uc_mcsize;      /* size of the machine context passed in */  
	_STRUCT_MCONTEXT        *uc_mcontext;   /* pointer to machine specific context */  
#ifdef _XOPEN_SOURCE  
	_STRUCT_MCONTEXT        __mcontext_data;  
#endif /* _XOPEN_SOURCE */  
};  
```  
if we include this WITHOUT _XOPEN_SOURCE, the struct will lack one of its field. But then, lately, we include sys/ucontext.h, which expects this macro to be defined, and it's actually defined, but it's too late, bc we already have "bad" version of the structure in current translation unit (second inclusion from sys/ucontext.h DOES NOT happen bc of include guard in /sys/_types/_ucontext.h). And then some weird issues happening (getcontext does memory overrun).  
  
One of examples of such bad include sequence:  
```  
In file included from /myproj/boost-src/boost/asio/append.hpp:24:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX13.0.sdk/usr/include/c++/v1/tuple:154:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX13.0.sdk/usr/include/c++/v1/__functional_base:22:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX13.0.sdk/usr/include/c++/v1/exception:83:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX13.0.sdk/usr/include/c++/v1/cstdlib:85:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX13.0.sdk/usr/include/c++/v1/stdlib.h:93:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX13.0.sdk/usr/include/stdlib.h:66:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX13.0.sdk/usr/include/sys/wait.h:109:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX13.0.sdk/usr/include/sys/signal.h:151:  
```  
and then signal.h on line 151 includes `#include <sys/_types/_ucontext.h>`.  
  
To summarize:   
1. Need fix for entry_func, bc makecontext does not work properly (at lease, on my MacOS EVEN with _XOPEN_SOURCE).  
2. Need to make sure that _XOPEN_SOURCE is defined for sources that are using boost.context directly or indirectly. If you building with CMake, just add ```target_compile_definitions(boost_context PUBLIC _XOPEN_SOURCE)```

---

## Comment 3

> Username: rwsfisker  
> Created_at: 2022-12-16 21:39:26 UTC  
> Url: https://github.com/boostorg/context/pull/213#issuecomment-1355660745  

> So sounds like this likely _doesn't_ solve #198?  
  
It does:  
```  
  
# clone boost, build boost_context  
git clone --recurse-submodules -j8 https://github.com/boostorg/boost.git  
cd boost  
boost_root=`pwd`  
./bootstrap.sh --with-libraries=context  
./b2 -a cxxflags='-std=c++17' context-impl=ucontext stage  
  
# clone / build crash reproducer  
git clone --recursive --branch crash-reproducer-2022.05.05 https://github.com/Geopipe/Cxx-Bidirectional-Coroutines.git  
mkdir Cxx-Bidirectional-Coroutines/build  
cd Cxx-Bidirectional-Coroutines/build  
cmake \  
 -DCMAKE_BUILD_TYPE=Debug \  
 -DCMAKE_CXX_FLAGS="-fsanitize=address -fsanitize=undefined -fsanitize-recover=all -DBOOST_USE_ASAN -DBOOST_USE_UCONTEXT" \  
 -DCMAKE_PREFIX_PATH=${boost_root}/stage/lib/cmake/Boost-1.82.0/ \  
 -S.. -B.  
cmake --build .  
  
# it crashes  
./example/testcoro # crashes  
  
# apply the patch / rebuild  
test_root=`pwd`  
cd ${boost_root}/libs/context  
curl https://github.com/boostorg/context/commit/cc106be672f54b71842aa6322fe45bf60ed1d3cc.diff | git apply -  
cd ${boost_root}  
./b2 -a cxxflags='-std=c++17' context-impl=ucontext stage  
  
# build it again, it works!  
cd ${test_root}  
cmake --build .  
% ./example/testcoro  
Fibs  
0  
1  
1  
2  
3  
5  
8  
13  
21  
34  
RBC  
1  
1  
1  
2  
3  
3  
4  
Noise  
Moo/0  
Moo/1  
Moo/2  
Moo/3  
Moo/4  
Moo/5  
Moo/6  
  
```

---

## Comment 4

> Username: rwsfisker  
> Created_at: 2022-12-16 22:38:56 UTC  
> Url: https://github.com/boostorg/context/pull/213#issuecomment-1355740150  

Verified Mac OSX 13, x86_64

---

## Comment 5

> Username: olk  
> Created_at: 2022-12-17 10:38:53 UTC  
> Url: https://github.com/boostorg/context/pull/213#issuecomment-1356167273  

ty

---
