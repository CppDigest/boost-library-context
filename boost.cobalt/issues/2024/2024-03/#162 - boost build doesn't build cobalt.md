# #162 - boost build doesn't build cobalt [Closed]

> Username: douaumont  
> Created at: 2024-03-17 11:29:11 UTC  
> Updated at: 2024-08-30 05:48:38 UTC  
> Closed at: 2024-03-17 13:18:19 UTC  
> Url: https://github.com/boostorg/cobalt/issues/162  

When I build boost libraries as described in [Getting Started](https://www.boost.org/doc/libs/1_84_0/more/getting_started/unix-variants.html) all libraries are built except `cobalt`. The toolset is `GCC 13`, OS is `Linux Mint 21.3`. Here are the steps I attempted:  
  
1. `/bootstrap.sh --with-libraries=cobalt`  
2.  `./b2`  
  
The last step yield the following output:  
  
```  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : x86 (cached) [1]  
  
Building the Boost C++ Libraries.  
  
  
    - cxx20_hdr_concepts       : no  (cached) [2]  
    - cxx20_hdr_concepts       : no  (cached) [3]  
    - cxx20_hdr_concepts       : no  (cached) [4]  
    - cxx20_hdr_concepts       : no  (cached) [5]  
  
[1] gcc-13  
[2] gcc-13/release/threading-multi/visibility-hidden  
[3] gcc-13/release/build-no/threading-multi/visibility-hidden  
[4] gcc-13/release/link-static/threading-multi/visibility-hidden  
[5] gcc-13/release/build-no/link-static/threading-multi/visibility-hidden  
  
Component configuration:  
  
    - atomic                   : not building  
    - chrono                   : not building  
    - cobalt                   : building  
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
    - headers                  : not building  
    - iostreams                : not building  
    - json                     : not building  
    - locale                   : not building  
    - log                      : not building  
    - math                     : not building  
    - mpi                      : not building  
    - nowide                   : not building  
    - program_options          : not building  
    - python                   : not building  
    - random                   : not building  
    - regex                    : not building  
    - serialization            : not building  
    - stacktrace               : not building  
    - system                   : not building  
    - test                     : not building  
    - thread                   : not building  
    - timer                    : not building  
    - type_erasure             : not building  
    - url                      : not building  
    - wave                     : not building  
  
...found 22 targets...  
  
The Boost C++ Libraries were successfully built!  
  
The following directory should be added to compiler include   
  
...  
  
The following directory should be added to linker library paths:  
  
...  
```  
But after that there is no `libboost_cobalt.a` nor anything like this.  I also tried using `cxxflags="-std=c++20 -fcoroutines"` but results were the same.  
  
So what am I doing wrong?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-03-17 12:02:32 UTC  
> Url: https://github.com/boostorg/cobalt/issues/162#issuecomment-2002431031  

Try 'cxxstd=20'.

---

## Comment 2

> Username: douaumont  
> Created at: 2024-03-17 13:18:19 UTC  
> Url: https://github.com/boostorg/cobalt/issues/162#issuecomment-2002463374  

Thanks, that worked just fine.

---

## Comment 3

> Username: sehe  
> Created at: 2024-04-24 22:25:22 UTC  
> Url: https://github.com/boostorg/cobalt/issues/162#issuecomment-2075952627  

Is this documented anywhere. I just spent an embarrassingly long time figuring this out. In fact, I don't think there's **anything** in the documentation even hinting at requirements for use? Because, since I couldn't find a CMake module nor a library artefact, I went to see whether the library has a header-only offering, but none of the words "linking", "building" or "header-only" seemed to appear in the docs.

---

## Comment 4

> Username: zhscn  
> Created at: 2024-08-30 05:48:37 UTC  
> Url: https://github.com/boostorg/cobalt/issues/162#issuecomment-2320133963  

Just encountered the same problem, I think this option should be included in the readme or documentation.
