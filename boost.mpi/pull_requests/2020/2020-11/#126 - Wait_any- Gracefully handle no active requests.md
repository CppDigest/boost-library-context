# #126 Wait_any: Gracefully handle no active requests [Open]

> Username: hirschsn  
> Created at: 2020-11-30 14:50:47 UTC  
> Updated at: 2021-01-21 15:43:56 UTC  
> Url: https://github.com/boostorg/mpi/pull/126  

This PR is analogous to #117.  
  
Current boost 1.74.0 deadlocks if wait_any is passed a range of inactive (null requests or already completed) requests.  
```c++  
#include <boost/mpi.hpp>  
#include <boost/mpi/nonblocking.hpp>  
#include <vector>  
  
  
int main()  
{  
	boost::mpi::environment env;  
	std::vector<boost::mpi::request> req(1);  
	boost::mpi::wait_any(req.begin(), req.end());  
}  
```  
(You may add this or derivatives of it as a test case under the boost license.)  
  
This PR fixes this issue by explicitly keeping track of the presence of an active request. If none is present, it returns an empty status and the iterator "last".  
  
Also, this PR changes the behavior for all trivial requests. Currently, boost-mpi throws an exception if wait_any is passed all trivial and inactive requests (nonblocking.hpp:96ff):  
```c++  
        // We don't have a notion of empty requests or status objects,  
        // so this is an error.  
        if (index == MPI_UNDEFINED)  
          boost::throw_exception(exception("MPI_Waitany", MPI_ERR_REQUEST));  
```  
Boost-MPI does actually have default constructed `status` objects that indicate an error status (cannot be queried for size, etc.). Also, MPI itself guarantees in this case to set the `status` parameter of the `MPI_Waitany` call to an empty status. So, this PR also changes this behavior to return the status and the iterator "last".

---

## Comment 1

> Username: aminiussi  
> Created_at: 2021-01-05 17:41:58 UTC  
> Url: https://github.com/boostorg/mpi/pull/126#issuecomment-754788554  

Hi, sorry for the delay.  
  
One request though: do you think you could add a test to check that case ?  
  
Thanks

---

## Comment 2

> Username: hirschsn  
> Created_at: 2021-01-21 15:43:56 UTC  
> Url: https://github.com/boostorg/mpi/pull/126#issuecomment-764733124  

@aminiussi Sorry for not including them in the first place.

---
