# #22 - Cannot build with GCC on macOS [Closed]

> Username: eschnett  
> Created at: 2021-07-14 14:38:10 UTC  
> Updated at: 2021-07-14 15:45:41 UTC  
> Closed at: 2021-07-14 15:45:41 UTC  
> Url: https://github.com/boostorg/property_map/issues/22  

I want to build Boost with GCC on macOS, using Spack. This fails with errors such as  
```  
gcc.compile.c++ bin.v2/libs/graph_parallel/build/gcc-11/release/cxxstd-17-iso/link-static/local-visibility-global/threading-multi/visibility-hidden/mpi_process_group.o  
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
```  
  
The complete build output, including this error, is [spack-build-out.txt](https://github.com/boostorg/property_map/files/6816852/spack-build-out.txt)  
  
I am building on mac OS 11.4 (Darwin 20.5.0) with GCC 11.1.0 (installed via Spack). I configured the `develop` branch of Boost with the options `cxxstd=17 +context +mpi`, which translates to `'./bootstrap.sh' '--prefix=/Users/eschnett/src/CarpetX/spack/opt/spack/darwin-bigsur-skylake/gcc-11.1.0/boost-develop-edadff2hnbldeqf66qcqam66ziao7p37' '--with-toolset=gcc' '--with-libraries=test,random,mpi,thread,date_time,system,regex,timer,chrono,filesystem,log,graph,iostreams,program_options,math,atomic,locale,wave,context,serialization,exception,graph_parallel' '--without-icu'` (see the top of the build output).  
  
There is indeed no file `boost/bind.hpp`. There is, however, a directory `boost/bind`, which contains a file `bind.hpp`.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-07-14 15:28:40 UTC  
> Url: https://github.com/boostorg/property_map/issues/22#issuecomment-879990499  

There *is* a [`boost/bind.hpp`](https://github.com/boostorg/bind/blob/34a3ee580c88d623c6bc9fe91460ef414dabfbe6/include/boost/bind.hpp) header, although it emits a deprecation warning and should be avoided. You probably have an incomplete Boost tree.

---

## Comment 2

> Username: eschnett  
> Created at: 2021-07-14 15:45:41 UTC  
> Url: https://github.com/boostorg/property_map/issues/22#issuecomment-880003836  

Thanks. It seems that the Spack recipe doesn't run `./b2 headers` after a git checkout.
