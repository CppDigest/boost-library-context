# #92 - UB in destruction order with global mpi::environment [Closed]

> Username: hirschsn  
> Created at: 2019-06-28 11:34:44 UTC  
> Updated at: 2019-10-22 14:40:03 UTC  
> Closed at: 2019-10-22 14:40:02 UTC  
> Url: https://github.com/boostorg/mpi/issues/92  

I noticed a problem in mpi::environment calling mpi_datatype_map::clear() of an already gone mpi_datatype_map. Consider the following *working program*:  
```c++  
#include <array>  
#include <vector>  
#include <boost/mpi/environment.hpp>  
#include <boost/mpi/communicator.hpp>  
#include <boost/mpi/collectives.hpp>  
  
int main()  
{  
    boost::mpi::environment env;  
    boost::mpi::communicator comm;  
  
    using Type = std::array<int, 3>;  
    Type p = {{0, 0, 0}};  
    std::vector<Type> ps;  
    // Using all_gather here, so there is no requirement on the number of processes.  
    boost::mpi::all_gather(comm, p, ps);  
}  
```  
The relevant thing that this program does is sends std::array<int, 3>s around. This creates an entry in the datatype_map with MPI_Type_create_struct.  
Now, let's move the environment out of main's scope, i.e.:  
```c++  
#include <array>  
#include <vector>  
#include <boost/mpi/environment.hpp>  
#include <boost/mpi/communicator.hpp>  
#include <boost/mpi/collectives.hpp>  
  
boost::mpi::environment env;  
  
int main()  
{  
    boost::mpi::communicator comm;  
  
    using Type = std::array<int, 3>;  
    Type p = {{0, 0, 0}};  
    std::vector<Type> ps;  
    boost::mpi::all_gather(comm, p, ps);  
}  
```  
The documentation [1] says: "In the vast majority of Boost.MPI programs, an instance of mpi::environment will be declared in main at the very beginning of the program." It does not forbid an environment in global scope. Nether does the documentation of environment [2].  
  
However, the second program segfaults on my machine (boost 1.70.0, OpenMPI 3.1.3) with:  
```  
*** Process received signal ***  
Signal: Segmentation fault (11)  
Signal code:  (128)  
Failing at address: (nil)  
[ 0] /lib/x86_64-linux-gnu/libc.so.6(+0x43f60)[0x7f4603819f60]  
[ 1] /usr/lib/x86_64-linux-gnu/libmpi.so.40(MPI_Type_free+0x3b)[0x7f4603c485bb]  
[ 2] /usr/lib/x86_64-linux-gnu/libboost_mpi.so.1.67.0(_ZN5boost3mpi6detail16mpi_datatype_map5clearEv+0x59)[0x7f4603d44d39]  
[ 3] /usr/lib/x86_64-linux-gnu/libboost_mpi.so.1.67.0(_ZN5boost3mpi11environmentD1Ev+0x5d)[0x7f4603d42c1d]  
[ 4] /lib/x86_64-linux-gnu/libc.so.6(+0x472ac)[0x7f460381d2ac]  
[ 5] /lib/x86_64-linux-gnu/libc.so.6(+0x473da)[0x7f460381d3da]  
[ 6] /lib/x86_64-linux-gnu/libc.so.6(__libc_start_main+0xf2)[0x7f46037fcb72]  
[ 7] ./test2(+0xcc8a)[0x55bf187efc8a]  
*** End of error message ***  
```  
  
After some debugging, I find that the difference is the order of destructors of the environment and the datatype_cache defined in mpi_datatype_cache.cpp:  
```  
mpi_datatype_map& mpi_datatype_cache()  
{  
    static mpi_datatype_map cache;  
    return cache;  
}  
```  
For version 1 it is:  
1. environment::~environment()  
2. mpi_datatype_map::~mpi_datatype_map()  
  
And for version 2 it is:  
1. mpi_datatype_map::~mpi_datatype_map()  
2. environment::~environment()  
  
So the static gets destructed before the global environment and, thus, environment::~environment() invokes UB. I suspect the reason for this is that the static is initialized on the first call to mpi_datatype_cache() which is *after* the initialization of the global environment variable in version 2. The reversed destruction, then, causes the above problem.  
  
I did a quick test for a quick fix, namely pulling the mpi_datatype_map out of mpi_datatype_cache()'s scope, i.e. changing mpi_datatype_cache() to this:  
```  
static mpi_datatype_map cache;  
mpi_datatype_map& mpi_datatype_cache()  
{  
    return cache;  
}  
```  
(Although its semantic is now different, I kept the `static` because the fix should not require external linkage.)  
With this, the segfault is gone and the destruction order in version 2 is the same as in version 1.  
  
I did not open a pull request yet because I wanted to discuss the fix first. Maybe someone has a better idea?  
  
[1] https://www.boost.org/doc/libs/1_70_0/doc/html/mpi/tutorial.html  
[2] https://www.boost.org/doc/libs/1_70_0/doc/html/boost/mpi/environment.html

---

## Comment 1

> Username: aminiussi  
> Created at: 2019-07-03 11:20:32 UTC  
> Url: https://github.com/boostorg/mpi/issues/92#issuecomment-508051959  

Thanks,  
I'll try to have a look next week, please ping me up if I skip.

---

## Comment 2

> Username: hirschsn  
> Created at: 2019-07-11 13:57:04 UTC  
> Url: https://github.com/boostorg/mpi/issues/92#issuecomment-510496698  

"Next week" is not over yet but I'll interpret it as a skip anyway. :-)  
So, ping @aminiussi

---

## Comment 3

> Username: aminiussi  
> Created at: 2019-07-13 18:03:16 UTC  
> Url: https://github.com/boostorg/mpi/issues/92#issuecomment-511142277  

Ok, finally have some time to look at it !!  
  
I suspect the "In the vast majority of Boost.MPI programs, an instance of mpi::environment will be declared in main at the very beginning of the program." was mean to be interpreted as "as opposed to another function." as it is mostly the boost MPI counter part of MPI_Init.  
Now, as MPI_Init and MPI_Finalize are supposed to be called by the user in the C version (so it cannot be before and after main) I'm not sure we want to encourage a global mpi::environment. If an MPI implementation is expecting the init/fini to be done in main (or in a function called from main) it can (according to the C++ standard, not really in real life with dynamic libraries, but anyway...) expect that all required global initialization have been done before MPI_Init and the MPI_Finalize will have been called before global destruction. We break that guarantee with a global mpi::environment.  
  
So I'm all for one less crash but think the documentation should be specifically clear that mpi::environment (and probably Boost.MPI objects potentially depending  on mpi::environment, which is most of them I guess)  should not be made global.  
  
But maybe I missed something.

---

## Comment 4

> Username: hirschsn  
> Created at: 2019-07-14 19:33:17 UTC  
> Updated at: 2019-07-14 19:34:16 UTC  
> Url: https://github.com/boostorg/mpi/issues/92#issuecomment-511229207  

> Now, as MPI_Init and MPI_Finalize are supposed to be called by the user in the C version (so it cannot be before and after main) I'm not sure we want to encourage a global mpi::environment.  
  
That is indeed a very good point.  
  
And there is probably a reason why nobody has stumbled over this yet. I actually wrote a unit test with several BOOST_AUTO_TEST_CASEs and for this, it seemed very convenient to have a global environment. Of course, now that you mention it, it seems like a rather bad idea. :)  
  
I would definitely vote for a clear statement about this in the documentation. I don't suppose there is much we can do to prevent static or global instances of `environment`. One possibility I see is forcing the user to supply `argc` and `argv`. But this is very bad as it would probably break most existing code.

---

## Comment 5

> Username: aminiussi  
> Created at: 2019-10-22 14:40:02 UTC  
> Url: https://github.com/boostorg/mpi/issues/92#issuecomment-544995612  

Documentation has been updated
