# #33 - One test fails after runtest graph_parallel submodule on x86 paltform. [Open]

> Username: qd1332543  
> Created at: 2021-09-02 09:13:17 UTC  
> Updated at: 2025-10-13 23:07:21 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/33  

Environment:  
VS 2019 + Windows Server 2016  
  
Issue description:  
One test fails after runtest graph_parallel submodule on x86 paltform. Could you please take a look?  
  
Reproduce steps:  
git clone ​https://github.com/boostorg/boost.git F:\gitP\boostorg\boost  
git -C "F:\gitP\boostorg\boost" submodule update --init --recursive  
.\b2 headers variant=release --build-dir=..\out\x86rel address-model=32  
.\b2 -j16 variant=release --build-dir=..\out\x86rel libs\graph_parallel\test  
[test_38.log](https://github.com/boostorg/graph_parallel/files/7097654/test_38.log)  
  
ErrorMessage:  
compile-c-c++ ..\out\x86rel\boost\bin.v2\libs\graph_parallel\example\msvc-14.2\release\threading-multi\breadth_first_search.obj  
breadth_first_search.cpp  
.\boost/mpi/config.hpp(22): fatal error C1083: Cannot open include file: 'mpi.h': No such file or directory (d:\a01\_work\4\s\src\vctools\Compiler\CxxFE\sl\p1\c\p0prepro.c:1964)  
  
...failed compile-c-c++ ..\out\x86rel\boost\bin.v2\libs\graph_parallel\example\msvc-14.2\release\threading-multi\breadth_first_search.obj...  
  
compile-c-c++ ..\out\x86rel\boost\bin.v2\libs\graph_parallel\example\msvc-14.2\release\threading-multi\dijkstra_shortest_paths.obj  
dijkstra_shortest_paths.cpp  
.\boost/mpi/config.hpp(22): fatal error C1083: Cannot open include file: 'mpi.h': No such file or directory (d:\a01\_work\4\s\src\vctools\Compiler\CxxFE\sl\p1\c\p0prepro.c:1964)  
  
...failed compile-c-c++ ..\out\x86rel\boost\bin.v2\libs\graph_parallel\example\msvc-14.2\release\threading-multi\dijkstra_shortest_paths.obj...

---

## Comment 1

> Username: jeking3  
> Created at: 2022-01-29 03:15:58 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/33#issuecomment-1024820144  

It looks like you did not provide the proper location to the mpi header.  
  
Take a look at https://github.com/boostorg/build/blob/dca10cf20759b0d194f8305356bf155cc5632c0e/src/tools/mpi.jam and see if that helps you get to a resolution.

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2025-10-13 23:07:21 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/33#issuecomment-3399353231  

@qd1332543 , if you still use this library, it is in desperate need of help to fix existing infrastructure problems, so I am calling on all users to please help if they can.
