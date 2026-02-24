# #117 Waitall: Handle null (default constructed) requests [Merged]

> Username: hirschsn  
> Created at: 2020-05-08 15:15:41 UTC  
> Updated at: 2020-06-19 11:54:37 UTC  
> Merged at: 2020-06-19 11:54:36 UTC  
> Closed at: 2020-06-19 11:54:37 UTC  
> Url: https://github.com/boostorg/mpi/pull/117  

Analogously to MPI_REQUEST_NULL being the neutral element to MPI_Wait(), a null request should gracefully be handled also by wait_all.  
  
Currently (boost 1.73) this deadlocks:  
```c++  
#include <boost/mpi.hpp>  
#include <boost/mpi/nonblocking.hpp>  
#include <vector>  
  
  
int main()  
{  
	boost::mpi::environment env;  
	std::vector<boost::mpi::request> req(1);  
	boost::mpi::wait_all(req.begin(), req.end());  
}  
```  
(You may add this or derivatives of it as a test case under the boost license.)  
  
This PR fixes this issue by treating inactive requests as completed.

---

## Comment 1

> Username: hirschsn  
> Created_at: 2020-05-08 15:18:01 UTC  
> Url: https://github.com/boostorg/mpi/pull/117#issuecomment-625866042  

Sorry, force-pushed because I somehow forgot some semicolons... :)

---

## Comment 2

> Username: hirschsn  
> Created_at: 2020-06-04 10:01:22 UTC  
> Url: https://github.com/boostorg/mpi/pull/117#issuecomment-638751081  

I just took a look at the issues. This might be related to or actually fix #109.

---

## Comment 3

> Username: aminiussi  
> Created_at: 2020-06-19 11:54:23 UTC  
> Url: https://github.com/boostorg/mpi/pull/117#issuecomment-646594804  

I added that specific test.   
  
I'm testing Intel and the merge shall go through.

---
