# #158 - Broadcast Boost.JSON objects [Open]

> Username: Arsennnic  
> Created at: 2024-06-03 08:17:55 UTC  
> Updated at: 2024-06-21 10:34:53 UTC  
> Url: https://github.com/boostorg/mpi/issues/158  

Hi, I wonder how to broadcast Boost.JSON objects with Boost.MPI library. I tried  
```c++  
#include <boost/json.hpp>  
#include <boost/mpi/collectives.hpp>  
#include <boost/mpi/communicator.hpp>  
#include <boost/mpi/environment.hpp>  
  
int main( int argc, char* argv[] )  
{  
    boost::mpi::environment env( argc, argv );  
    boost::mpi::communicator world;  
    boost::json::object json;  
    boost::mpi::broadcast( world, json, 0 );  
    return 0;  
}  
```  
  
and got the error `'class boost::json::object' has no member named 'serialize'`.  
  
I guess I missed some headers like `<boost/serialization/string.hpp>` or `<boost/serialization/vector.hpp>`, but I don't know which ones should be included.

---

## Comment 1

> Username: aminiussi  
> Created at: 2024-06-21 10:34:51 UTC  
> Url: https://github.com/boostorg/mpi/issues/158#issuecomment-2182489573  

Sorry for the delay.  
  
There is nothing done specificaly for json trees. It's considered a user type and as such requires it's own serialisation implementation as described in   
https://www.boost.org/doc/libs/1_85_0/doc/html/mpi/tutorial.html#mpi.tutorial.user_data_types  
  
Regards
