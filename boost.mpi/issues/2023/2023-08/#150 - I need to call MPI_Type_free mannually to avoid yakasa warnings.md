# #150 - I need to call MPI_Type_free mannually to avoid yakasa warnings. [Closed]

> Username: xiechao06  
> Created at: 2023-08-28 08:18:32 UTC  
> Updated at: 2023-09-18 16:21:42 UTC  
> Closed at: 2023-09-18 16:21:42 UTC  
> Url: https://github.com/boostorg/mpi/issues/150  

In this code sample, I send a bool type:  
  
```c++  
#include <boost/mpi.hpp>  
#include <iostream>  
#include <string>  
  
namespace mpi = boost::mpi;  
  
int main(int argc, char **argv)  
{  
    mpi::communicator world;  
    mpi::environment env;  
  
    auto rank = world.rank();  
    bool done;  
  
    if (rank == 0)  
    {  
        world.send(1, 0, true);  
    }  
    else  
    {  
        world.recv(0, 0, done);  
        printf("%d\n", done);  
    }  
    // I must free the type by hand, otherwise yakasa complains  
    // auto type = mpi::get_mpi_datatype(done);  
    // MPI_Type_free(&type);  
  
    return 0;  
}  
```  
If I run it, every thing works except some warnings:  
  
```  
1  
[WARNING] yaksa: 1 leaked handle pool objects  
[WARNING] yaksa: 1 leaked handle pool objects  
```  
  
It seems that datatype_cache is not cleared properly.   
  
  
## environment  
  
* <img width="695" alt="image" src="https://github.com/boostorg/mpi/assets/2888536/b3bc9b4a-87da-4130-88cd-ac15cd8aa310">  
* <img width="700" alt="image" src="https://github.com/boostorg/mpi/assets/2888536/a1cf72da-aac5-44a6-a347-74bb9e6b1ead">  
* ubuntu 22.04  
  
## the sample project is https://github.com/xiechao06/send_bool_boost_mpi

---

## Comment 1

> Username: aminiussi  
> Created at: 2023-09-18 15:51:59 UTC  
> Updated at: 2023-09-18 16:13:16 UTC  
> Url: https://github.com/boostorg/mpi/issues/150#issuecomment-1723787058  

Since there is no MPI bool type, there is special treatment in Boost.MPI to create one when needed. But the type is not registered for cleanup nor specifically deleted yet.
