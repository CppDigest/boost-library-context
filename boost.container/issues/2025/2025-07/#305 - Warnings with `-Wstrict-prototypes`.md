# #305 - Warnings with `-Wstrict-prototypes` [Closed]

> Username: kou  
> Created at: 2025-07-07 03:16:55 UTC  
> Updated at: 2025-08-25 22:35:32 UTC  
> Closed at: 2025-08-25 22:35:32 UTC  
> Url: https://github.com/boostorg/container/issues/305  

https://github.com/boostorg/container/blob/develop/include/boost/container/detail/alloc_lib.h has some no argument prototype declarations such as: https://github.com/boostorg/container/blob/e8baf848bc426022dffbc814db8e1261d6033d4b/include/boost/container/detail/alloc_lib.h#L257  
  
`-Wstrict-prototypes` warns them because  https://github.com/boostorg/container/blob/develop/src/alloc_lib.c is built as C not C++.  
  
For example:  
  
https://dev.azure.com/ursacomputing/crossbow/_build/results?buildId=70216&view=logs&j=0da5d1d9-276d-5173-c4c4-9d4d4ed14fdb&t=3367cfe7-6aa5-5a88-c888-e6814d74f6eb&l=1281  
  
```text  
[  7%] Building C object _deps/boost-build/libs/container/CMakeFiles/boost_container.dir/src/alloc_lib.c.o  
In file included from /tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/src/dlmalloc_ext_2_8_6.c:11,  
                 from /tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/src/alloc_lib.c:24:  
/tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/include/boost/container/detail/alloc_lib.h:257:1: warning: function declaration isn’t a prototype [-Wstrict-prototypes]  
  257 | size_t boost_cont_footprint();  
      | ^~~~~~  
/tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/include/boost/container/detail/alloc_lib.h:259:1: warning: function declaration isn’t a prototype [-Wstrict-prototypes]  
  259 | size_t boost_cont_allocated_memory();  
      | ^~~~~~  
/tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/include/boost/container/detail/alloc_lib.h:263:1: warning: function declaration isn’t a prototype [-Wstrict-prototypes]  
  263 | int boost_cont_all_deallocated();  
      | ^~~  
/tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/include/boost/container/detail/alloc_lib.h:265:1: warning: function declaration isn’t a prototype [-Wstrict-prototypes]  
  265 | boost_cont_malloc_stats_t boost_cont_malloc_stats();  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~  
/tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/include/boost/container/detail/alloc_lib.h:267:1: warning: function declaration isn’t a prototype [-Wstrict-prototypes]  
  267 | size_t boost_cont_in_use_memory();  
      | ^~~~~~  
/tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/include/boost/container/detail/alloc_lib.h:282:1: warning: function declaration isn’t a prototype [-Wstrict-prototypes]  
  282 | int boost_cont_malloc_check();  
      | ^~~  
/tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/include/boost/container/detail/alloc_lib.h:293:1: warning: function declaration isn’t a prototype [-Wstrict-prototypes]  
  293 | void *boost_cont_sync_create();  
      | ^~~~  
/tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/include/boost/container/detail/alloc_lib.h:301:1: warning: function declaration isn’t a prototype [-Wstrict-prototypes]  
  301 | int boost_cont_global_sync_lock();  
      | ^~~  
/tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/include/boost/container/detail/alloc_lib.h:303:1: warning: function declaration isn’t a prototype [-Wstrict-prototypes]  
  303 | void boost_cont_global_sync_unlock();  
      | ^~~~  
In file included from /tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/src/dlmalloc_ext_2_8_6.c:52:  
/tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/src/dlmalloc_2_8_6.c:999:1: warning: function declaration isn’t a prototype [-Wstrict-prototypes]  
  999 | DLMALLOC_EXPORT size_t dlmalloc_footprint_limit();  
      | ^~~~~~~~~~~~~~~  
/tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/src/dlmalloc_ext_2_8_6.c:1207:8: warning: function declaration isn’t a prototype [-Wstrict-prototypes]  
 1207 | size_t boost_cont_footprint()  
      |        ^~~~~~~~~~~~~~~~~~~~  
/tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/src/dlmalloc_ext_2_8_6.c:1212:8: warning: function declaration isn’t a prototype [-Wstrict-prototypes]  
 1212 | size_t boost_cont_allocated_memory()  
      |        ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
/tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/src/dlmalloc_ext_2_8_6.c:1258:5: warning: function declaration isn’t a prototype [-Wstrict-prototypes]  
 1258 | int boost_cont_all_deallocated()  
      |     ^~~~~~~~~~~~~~~~~~~~~~~~~~  
/tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/src/dlmalloc_ext_2_8_6.c:1261:27: warning: function declaration isn’t a prototype [-Wstrict-prototypes]  
 1261 | boost_cont_malloc_stats_t boost_cont_malloc_stats()  
      |                           ^~~~~~~~~~~~~~~~~~~~~~~  
/tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/src/dlmalloc_ext_2_8_6.c:1274:8: warning: function declaration isn’t a prototype [-Wstrict-prototypes]  
 1274 | size_t boost_cont_in_use_memory()  
      |        ^~~~~~~~~~~~~~~~~~~~~~~~  
/tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/src/dlmalloc_ext_2_8_6.c:1343:5: warning: function declaration isn’t a prototype [-Wstrict-prototypes]  
 1343 | int boost_cont_malloc_check()  
      |     ^~~~~~~~~~~~~~~~~~~~~~~  
/tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/src/dlmalloc_ext_2_8_6.c:1441:7: warning: function declaration isn’t a prototype [-Wstrict-prototypes]  
 1441 | void *boost_cont_sync_create()  
      |       ^~~~~~~~~~~~~~~~~~~~~~  
/tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/src/dlmalloc_ext_2_8_6.c:1467:5: warning: function declaration isn’t a prototype [-Wstrict-prototypes]  
 1467 | int boost_cont_global_sync_lock()  
      |     ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
/tmp/Rtmp2F9BfP/working_dir/Rtmp00O9rj/file6ae6a658872/_deps/boost-src/libs/container/src/dlmalloc_ext_2_8_6.c:1475:6: warning: function declaration isn’t a prototype [-Wstrict-prototypes]  
 1475 | void boost_cont_global_sync_unlock()  
      |      ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```
