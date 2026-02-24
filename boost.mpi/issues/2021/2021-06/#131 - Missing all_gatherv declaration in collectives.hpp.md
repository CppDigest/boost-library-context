# #131 - Missing all_gatherv declaration in collectives.hpp [Closed]

> Username: stubbsda  
> Created at: 2021-06-14 16:34:12 UTC  
> Updated at: 2022-06-02 13:12:15 UTC  
> Closed at: 2022-06-02 13:12:15 UTC  
> Url: https://github.com/boostorg/mpi/issues/131  

The header file collectives.hpp doesn't contain any declarations for the all_gatherv functions (introduced in version 1.67), so that any use of boost::mpi::all_gatherv has to explicitly load the relevant header file,  
#include <boost/mpi/collectives/all_gatherv.hpp>  
Would it be possible to include the declaration of all_gatherv functions in collectives.hpp so that a single   
#include <boost/mpi.hpp>  
suffices for software using all_gatherv?

---

## Comment 1

> Username: aminiussi  
> Created at: 2022-06-02 13:12:13 UTC  
> Url: https://github.com/boostorg/mpi/issues/131#issuecomment-1144847740  

Just merged in develop
