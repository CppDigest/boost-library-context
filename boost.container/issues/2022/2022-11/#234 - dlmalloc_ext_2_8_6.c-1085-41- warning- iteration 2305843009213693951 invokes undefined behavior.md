# #234 - dlmalloc_ext_2_8_6.c:1085:41: warning: iteration 2305843009213693951 invokes undefined behavior [-Waggressive-loop-optimizations] [Closed]

> Username: mojca  
> Created at: 2022-11-12 07:03:07 UTC  
> Updated at: 2024-12-23 18:35:58 UTC  
> Closed at: 2024-12-23 18:35:57 UTC  
> Url: https://github.com/boostorg/container/issues/234  

I'm building a project with boost 1.80.0 as an external dependency, obtained via `FetchContent` as part of a CMake build.  
I'm using gcc-12 on Ubuntu-22.04 and we are trying to get "treat warnings as errors" builds go through.  
  
This is the warning I get:  
```  
[1/675] Building C object _deps/boost-build/libs/container/CMakeFiles/boost_container.dir/src/alloc_lib.c.o  
In file included from ../__external/src/boost/libs/container/src/dlmalloc_ext_2_8_6.c:52,  
                 from ../__external/src/boost/libs/container/src/alloc_lib.c:24:  
In function ‘internal_multialloc_arrays’,  
    inlined from ‘boost_cont_multialloc_arrays’ at ../__external/src/boost/libs/container/src/dlmalloc_ext_2_8_6.c:1112:13:  
../__external/src/boost/libs/container/src/dlmalloc_ext_2_8_6.c:1085:41: warning: iteration 2305843009213693951 invokes undefined behavior [-Waggressive-loop-optimizations]  
 1085 |                size = request2size(sizes[i]*element_size);  
      |                                         ^  
../__external/src/boost/libs/container/src/dlmalloc_2_8_6.c:2231:6: note: in definition of macro ‘request2size’  
 2231 |   (((req) < MIN_REQUEST)? MIN_CHUNK_SIZE : pad_request(req))  
      |      ^~~  
../__external/src/boost/libs/container/src/dlmalloc_ext_2_8_6.c:1083:24: note: within this loop  
 1083 |             for(++i; i != next_i; ++i) {  
      |                      ~~^~~~~~~~~  
```

---

## Comment 1

> Username: valiko-ua  
> Created at: 2024-12-23 06:02:10 UTC  
> Updated at: 2024-12-23 06:09:38 UTC  
> Url: https://github.com/boostorg/container/issues/234#issuecomment-2558968711  

The same warning for Boost 1.87.0, gcc-8.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-12-23 18:33:58 UTC  
> Url: https://github.com/boostorg/container/issues/234#issuecomment-2560142667  

There are quite a lot false positives with "Waggressive-loop-optimizations" (e.g. https://gcc.gnu.org/bugzilla/show_bug.cgi?id=102513), and the iteration is safe because the maximum allocation size is checked at the start of the function. So we will selectively disable this warning in this file.
