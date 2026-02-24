# #39 Replace boost::serialization::detail::get_data function. [Merged]

> Username: jwakely  
> Created at: 2017-01-26 22:33:35 UTC  
> Updated at: 2017-07-19 20:09:10 UTC  
> Merged at: 2017-02-23 02:18:58 UTC  
> Closed at: 2017-02-23 02:18:58 UTC  
> Url: https://github.com/boostorg/mpi/pull/39  

Several Boost.MPI headers include `<boost/serialization/detail/get_data.hpp>` which was removed by https://github.com/boostorg/serialization/commit/d558b6da917ecae1036adf9b22a0741c78f627ff  
  
This causes multiple build failures with the current snapshot at http://downloads.sourceforge.net/boost/boost_1_63_0.tar.bz2  
  
````  
    "g++"  -g -O2 -g -pipe -Wall -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -specs=/usr/lib/rpm/redhat/redhat-hardened-cc1 -m64 -mtune=generic -fn  
o-strict-aliasing -Wno-unused-local-typedefs -pthread -fPIC  -DBOOST_ALL_NO_LIB=1 -DBOOST_GRAPH_DYN_LINK=1 -DBOOST_GRAPH_NO_LIB=1 -DNDEBUG  -I"." -I"/usr/include/openmpi-x86_64" -I"libs/graph_parallel/src" -c -o "openmpi-x86_64/boost/bin  
.v2/libs/graph_parallel/build/gcc-6.3.1/release/debug-symbols-on/pch-off/threading-multi/mpi_process_group.o" "libs/graph_parallel/src/mpi_process_group.cpp"  
  
In file included from ./boost/mpi/detail/mpi_datatype_oarchive.hpp:18:0,  
                from ./boost/mpi/detail/mpi_datatype_cache.hpp:13,  
                from ./boost/mpi/datatype.hpp:27,  
                from ./boost/mpi/communicator.hpp:22,  
                from ./boost/mpi/collectives.hpp:21,  
                from ./boost/mpi.hpp:23,  
                from ./boost/graph/distributed/mpi_process_group.hpp:30,  
                from libs/graph_parallel/src/mpi_process_group.cpp:14:  
./boost/mpi/detail/mpi_datatype_primitive.hpp:28:51: fatal error: boost/serialization/detail/get_data.hpp: No such file or directory  
#include <boost/serialization/detail/get_data.hpp>  
                                                  ^  
compilation terminated.  
````  
There are other failures due to `packed_oprimitive` and `packed_iprimitive` also using the missing header.  
  
Since only one function from the header is needed the simplest thing is to just define that function locally in the classes that need it.

---

## Comment 1

> Username: aminiussi  
> Created_at: 2017-02-22 15:41:04 UTC  
> Url: https://github.com/boostorg/mpi/pull/39#issuecomment-281706125  

Hi, #42 is supposed to address that issue by removing the call to get_data. The vector::data provide the service and it's only use on data member of the boost::mpi classes.

---

## Comment 2

> Username: aminiussi  
> Created_at: 2017-02-22 15:42:20 UTC  
> Url: https://github.com/boostorg/mpi/pull/39#issuecomment-281706504  

#42 is pending review, if the review does not come between a couple of day, I'll merge it anyway.  
Thanks

---

## Comment 3

> Username: jwakely  
> Created_at: 2017-07-01 13:27:15 UTC  
> Url: https://github.com/boostorg/mpi/pull/39#issuecomment-312432187  

I'm seeing this error in Boost 1.64.0, was this not fixed on master for the release?

---

## Comment 4

> Username: aminiussi  
> Created_at: 2017-07-19 20:09:10 UTC  
> Url: https://github.com/boostorg/mpi/pull/39#issuecomment-316501921  

It's been merged as part of #49

---
