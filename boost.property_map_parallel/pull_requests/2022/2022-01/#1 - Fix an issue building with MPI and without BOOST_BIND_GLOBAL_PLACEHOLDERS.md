# #1 Fix an issue building with MPI and without BOOST_BIND_GLOBAL_PLACEHOLDERS [Closed]

> Username: jeking3  
> Created at: 2022-01-28 03:06:17 UTC  
> Updated at: 2022-01-28 04:47:52 UTC  
> Closed at: 2022-01-28 04:47:52 UTC  
> Url: https://github.com/boostorg/property_map_parallel/pull/1  

Using [bdde](https://github.com/jeking3/bdde) I have a docker-based build environment that enables optional features like MPI.  It looks like boost/bind changed such that you need to define `BOOST_BIND_GLOBAL_PLACEHOLDERS` if you want to include `<boost/bind.hpp>`, or you need to include `<boost/bind/bind.hpp>`; so I changed the code to the latter.  
  
Without the fix the following error would occur:  
  
```  
gcc.compile.c++ bin.v2/libs/graph_parallel/build/gcc-9/release/link-static/local-visibility-global/threading-multi/visibility-hidden/mpi_process_group.o  
In file included from ./boost/property_map/parallel/distributed_property_map.hpp:689,  
                 from ./boost/property_map/parallel/parallel_property_maps.hpp:30,  
                 from ./boost/property_map/property_map.hpp:602,  
                 from ./boost/graph/properties.hpp:19,  
                 from ./boost/mpi/graph_communicator.hpp:30,  
                 from ./boost/mpi.hpp:27,  
                 from ./boost/graph/distributed/mpi_process_group.hpp:30,  
                 from libs/graph_parallel/src/mpi_process_group.cpp:14:  
./boost/property_map/parallel/impl/distributed_property_map.ipp:14:10: fatal error: boost/bind.hpp: No such file or directory  
   14 | #include <boost/bind.hpp>  
      |          ^~~~~~~~~~~~~~~~  
compilation terminated.  
  
    "g++"   -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=default  -DBOOST_ALL_NO_LIB=1 -DBOOST_GRAPH_NO_LIB=1 -DNDEBUG  -I"." -I"/usr/lib/x86_64-linux-gnu/openmpi/include" -I"/usr/lib/x86_64-linux-gnu/openmpi/include/openmpi" -I"libs/graph_parallel/src"  -c -o "bin.v2/libs/graph_parallel/build/gcc-9/release/link-static/local-visibility-global/threading-multi/visibility-hidden/mpi_process_group.o" "libs/graph_parallel/src/mpi_process_group.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/graph_parallel/build/gcc-9/release/link-static/local-visibility-global/threading-multi/visibility-hidden/mpi_process_group.o...  
```

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-01-28 03:12:35 UTC  
> Url: https://github.com/boostorg/property_map_parallel/pull/1#issuecomment-1023845885  

This is not an issue with `BOOST_BIND_GLOBAL_PLACEHOLDERS`; a macro not being defined can't cause a header to fail to be found. It's an issue in Boost.Build/b2, caused by the fact that the include is in a file with extension `.ipp`. https://github.com/boostorg/build/issues/736

---

## Comment 2

> Username: jeking3  
> Created_at: 2022-01-28 03:24:53 UTC  
> Url: https://github.com/boostorg/property_map_parallel/pull/1#issuecomment-1023851232  

@pdimov thanks - I've been away for too long, clearly :)  Thanks for pointing be at the root cause.  Is this change a valid workaround or should I close it?

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-01-28 03:30:35 UTC  
> Url: https://github.com/boostorg/property_map_parallel/pull/1#issuecomment-1023853595  

It looks like `boost::bind` isn't used at all, so the include should probably be removed altogether.

---
