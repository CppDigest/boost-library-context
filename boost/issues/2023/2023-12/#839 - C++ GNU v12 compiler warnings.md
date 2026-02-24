# #839 - C++ GNU v12 compiler warnings [Open]

> Username: melroy89  
> Created at: 2023-12-22 01:38:26 UTC  
> Updated at: 2023-12-22 01:38:26 UTC  
> Url: https://github.com/boostorg/boost/issues/839  

I just want to mention I did notice the following warnings when compiling my project with boost. I use compile flags: `-Wall -Wextra -pedantic`.. Maybe these warning below can be resolved easily? I think the other two are just notes.  
  
```sh  
[19/23] Building C object _deps/boost-build/libs/container/CMakeFiles/boost_container.dir/src/alloc_lib.c.o  
In file included from /builds/melroy/rambam/build_prod/_deps/boost-src/libs/container/src/dlmalloc_ext_2_8_6.c:52,  
                 from /builds/melroy/rambam/build_prod/_deps/boost-src/libs/container/src/alloc_lib.c:24:  
In function ‘internal_multialloc_arrays’,  
    inlined from ‘boost_cont_multialloc_arrays’ at /builds/melroy/rambam/build_prod/_deps/boost-src/libs/container/src/dlmalloc_ext_2_8_6.c:1112:13:  
/builds/melroy/rambam/build_prod/_deps/boost-src/libs/container/src/dlmalloc_ext_2_8_6.c:1085:41: warning: iteration 2305843009213693951 invokes undefined behavior [-Waggressive-loop-optimizations]  
 1085 |                size = request2size(sizes[i]*element_size);  
      |                                         ^  
/builds/melroy/rambam/build_prod/_deps/boost-src/libs/container/src/dlmalloc_2_8_6.c:2231:6: note: in definition of macro ‘request2size’  
 2231 |   (((req) < MIN_REQUEST)? MIN_CHUNK_SIZE : pad_request(req))  
      |      ^~~  
/builds/melroy/rambam/build_prod/_deps/boost-src/libs/container/src/dlmalloc_ext_2_8_6.c:1083:24: note: within this loop  
 1083 |             for(++i; i != next_i; ++i) {  
      |                      ~~^~~~~~~~~  
```  
  
I think `sizes[i]` is a potentially very large unsigned integer. This multiplication could overflow the result, causing the request2size function to return an incorrect value. This incorrect value could then lead to undefined behavior in the caller of the `internal_multialloc_arrays` function. Correct me if I'm wrong. You need to check `sizes` to avoid overflow.
