# #53 Fix error in boost::mpi::wait_any (possible deadlock) [Closed]

> Username: hirschsn  
> Created at: 2018-03-08 15:07:41 UTC  
> Updated at: 2018-04-09 12:39:43 UTC  
> Closed at: 2018-03-13 14:20:55 UTC  
> Url: https://github.com/boostorg/mpi/pull/53  

When using nonblocking communication with serialized data, boost::mpi::wait_any can deadlock if one calls wait_any until all data is received. The problem is that `current->m_requests[0]` can be `MPI_REQUEST_NULL` but the data has not actually been received, i.e. `current->m_requests[1] != MPI_REQUEST_NULL`.  
  
This is because `MPI_Test` as called from `handle_serialized_irecv` sets the request it is given to `MPI_REQUEST_NULL` if the communication has completed. If, however, only the count has already been received by MPI but not the data itself the `MPI_Test` call on `m_requests[1]` returns `flag = false` and leaves an open `boost::request` where `m_requests[0] == MPI_REQUEST_NULL` and `m_requests[1] != MPI_REQUEST_NULL`.  
  
I propose to fix this by querying if any of the two MPI_Request members is not `MPI_REQUEST_NULL`. However, since I have only looked at this particular problem, I might be missing some other features/details that actually need a more elaborate conditional.

---

## Comment 1

> Username: aminiussi  
> Created_at: 2018-03-08 15:34:21 UTC  
> Url: https://github.com/boostorg/mpi/pull/53#issuecomment-371523286  

Thanks for the patch,  
  
I need to have a close look at this, since it reminds me of a fix for another deadlock condition.  
  
Did it trigger a deadlock on one of the tests and if yes, which one and with which underlying MPI implementation ?  
  
Thx

---

## Comment 2

> Username: hirschsn  
> Created_at: 2018-03-08 15:49:05 UTC  
> Url: https://github.com/boostorg/mpi/pull/53#issuecomment-371528160  

No, I did not look at the tests because I suspected that the problem is not caught by any test.  
  
I triggered the deadlock with the following simple all-to-all nonblocking communication code and OpenMPI 1.10.2 and 2.1.1 (however, should probably be reproducible on any MPI implementation).  
  
```  
#include <boost/mpi.hpp>  
#include <boost/mpi/nonblocking.hpp>  
#include <boost/serialization/string.hpp>  
#include <string>  
  
namespace mpi = boost::mpi;  
  
int main()  
{  
    mpi::environment env;  
    mpi::communicator world;  
  
    std::vector<std::string> ss(world.size());  
    std::vector<mpi::request> rreqs;  
  
    for (int i = 0; i < world.size(); ++i)  
        rreqs.push_back(world.irecv(i, 0, ss[i]));  
  
    std::string s = "Hello, World!";  
    std::vector<mpi::request> sreqs;  
    for (int i = 0; i < world.size(); ++i)  
        sreqs.push_back(world.isend(i, 0, s));  
  
    mpi::status status;  
    decltype(rreqs.begin()) it;  
    for (int i = 0; i < world.size(); ++i)  
        std::tie(status, it) = mpi::wait_any(std::begin(rreqs), std::end(rreqs));  
    // Doing wait_any world.size() times should have the same effect as wait_all.  
  
    mpi::wait_all(std::begin(sreqs), std::end(sreqs));  
  
    return 0;  
}  
```  
  
Since the deadlock is not deterministic (depends on the underlying communication), you might need to run the code several times.

---

## Comment 3

> Username: aminiussi  
> Created_at: 2018-03-12 10:24:39 UTC  
> Url: https://github.com/boostorg/mpi/pull/53#issuecomment-372260708  

I can reproduce the problem with Intel's MPI, but I'm pretty sure there are situations where the proposed patch would randomly fail.   
  
The slightly modified test produces errors on my platform, I'm looking into those.  
  
```  
#include <boost/mpi.hpp>  
#include <boost/mpi/nonblocking.hpp>  
#include <boost/serialization/string.hpp>  
#include <string>  
  
namespace mpi = boost::mpi;  
  
int main()  
{  
  mpi::environment env;  
  mpi::communicator world;  
  
  std::vector<std::string> ss(world.size());  
  std::vector<mpi::request> rreqs;  
  
  for (int i = 0; i < world.size(); ++i)  
    rreqs.push_back(world.irecv(i, i, ss[i]));  
  
  std::string s = "Hello, World!";  
  std::vector<mpi::request> sreqs;  
  for (int i = 0; i < world.size(); ++i)  
    sreqs.push_back(world.isend(i, world.rank(), s));  
  
  mpi::status status;  
  decltype(rreqs.begin()) it;  
  for (int i = 0; i < world.size(); ++i) {  
    std::ostringstream fmt;  
    std::tie(status, it) = mpi::wait_any(std::begin(rreqs), std::end(rreqs));  
    fmt << "At proc " << world.rank() << " Received from " << status.source()  
        << " a message with tag " << status.tag()  
        << " and error code " << status.error() ;  
    char err[MPI_MAX_ERROR_STRING];  
    int len;  
    MPI_Error_string(status.error(), err, &len);  
    fmt << ": " << err << '\n';  
    std::cout << fmt.str();  
    rreqs.erase(it);  
  }  
  // Doing wait_any world.size() times should have the same effect as wait_all.                                                                                                                                      
  
  //mpi::wait_all(std::begin(sreqs), std::end(sreqs));                                                                                                                                                               
  return 0;  
}  
  
```  
produces:  
```  
[alainm@pollux a2a]$ mpiexec.hydra -n 2 ./truc  # erro messages, but returns  
At proc 0 Received from 0 a message with tag 0 and error code -138763752: Undefined dynamic error code  
At proc 0 Received from 1 a message with tag 1 and error code 1: Invalid buffer pointer  
At proc 1 Received from 0 a message with tag 0 and error code -138763752: Undefined dynamic error code  
At proc 1 Received from 1 a message with tag 1 and error code 1: Invalid buffer pointer  
[alainm@pollux a2a]$ mpiexec.hydra -n 3 ./truc # hangs  
At proc 0 Received from 0 a message with tag 0 and error code -138763752: Undefined dynamic error code  
At proc 1 Received from 1 a message with tag 1 and error code -138763752: Undefined dynamic error code  
At proc 1 Received from 2 a message with tag 2 and error code 1: Invalid buffer pointer  
At proc 0 Received from 1 a message with tag 1 and error code 1: Invalid buffer pointer  
At proc 0 Received from 2 a message with tag 2 and error code 1: Invalid buffer pointer  
At proc 2 Received from 1 a message with tag 1 and error code -138763752: Undefined dynamic error code  
At proc 2 Received from 2 a message with tag 2 and error code 1: Invalid buffer pointer  
  
  
```

---

## Comment 4

> Username: aminiussi  
> Created_at: 2018-03-12 10:27:16 UTC  
> Url: https://github.com/boostorg/mpi/pull/53#issuecomment-372261433  

And we're missing a error string converter...

---

## Comment 5

> Username: aminiussi  
> Created_at: 2018-03-12 11:57:05 UTC  
> Url: https://github.com/boostorg/mpi/pull/53#issuecomment-372285482  

My test program is invalid, please ignore.

---

## Comment 6

> Username: aminiussi  
> Created_at: 2018-03-12 12:09:01 UTC  
> Url: https://github.com/boostorg/mpi/pull/53#issuecomment-372288336  

So yes, it fails to detect a pending non trivial request when the size have been received.

---

## Comment 7

> Username: aminiussi  
> Created_at: 2018-03-12 15:36:31 UTC  
> Url: https://github.com/boostorg/mpi/pull/53#issuecomment-372353522  

Ok, now I remember why the bad memories:   
ed4852863a59b8ae270bddac949f553a8200146a

---

## Comment 8

> Username: aminiussi  
> Created_at: 2018-03-12 15:38:22 UTC  
> Url: https://github.com/boostorg/mpi/pull/53#issuecomment-372354205  

I have a fix (same as yours with some comments for "next time").  
I'd like to add a test before merging.

---

## Comment 9

> Username: aminiussi  
> Created_at: 2018-03-13 10:01:52 UTC  
> Url: https://github.com/boostorg/mpi/pull/53#issuecomment-372610827  

see [waitany53](../tree/waitany53)

---

## Comment 10

> Username: aminiussi  
> Created_at: 2018-03-13 14:20:05 UTC  
> Url: https://github.com/boostorg/mpi/pull/53#issuecomment-372681422  

Fixed in #54

---

## Comment 11

> Username: aminiussi  
> Created_at: 2018-04-09 12:39:42 UTC  
> Url: https://github.com/boostorg/mpi/pull/53#issuecomment-379737476  

Hi,  
  
Is anoyone available to have a look at   
https://github.com/boostorg/mpi/pull/56 ? which is a modified version of   
https://github.com/boostorg/mpi/pull/53.  
  
Thanks  
  
Alain  
  
On 08/03/2018 16:49, Steffen Hirschmann wrote:  
>  
> No, I did not look at the tests because I suspected that the problem   
> is not caught by any test.  
>  
> I triggered the deadlock with the following simple all-to-all   
> nonblocking communication code and OpenMPI 1.10.2 and 2.1.1 (however,   
> should probably be reproducible on any MPI implementation).  
>  
> |#include <boost/mpi.hpp> #include <boost/mpi/nonblocking.hpp> #include   
> <boost/serialization/string.hpp> #include <string> namespace mpi =   
> boost::mpi; int main() { mpi::environment env; mpi::communicator   
> world; std::vector<std::string> ss(world.size());   
> std::vector<mpi::request> rreqs; for (int i = 0; i < world.size();   
> ++i) rreqs.push_back(world.irecv(i, 0, ss[i])); std::string s =   
> "Hello, World!"; std::vector<mpi::request> sreqs; for (int i = 0; i <   
> world.size(); ++i) sreqs.push_back(world.isend(i, 0, s)); mpi::status   
> status; decltype(rreqs.begin()) it; for (int i = 0; i < world.size();   
> ++i) std::tie(status, it) = mpi::wait_any(std::begin(rreqs),   
> std::end(rreqs)); // Doing wait_any world.size() times should have the   
> same effect as wait_all. mpi::wait_all(std::begin(sreqs),   
> std::end(sreqs)); return 0; } |  
>  
> Since the deadlock is not deterministic (depends on the underlying   
> communication), you might need to run the code several times.  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/mpi/pull/53#issuecomment-371528160>, or   
> mute the thread   
> <https://github.com/notifications/unsubscribe-auth/AAhsjNQKfmI9zubdqTLn5qUoLTy73a2wks5tcVLygaJpZM4Si05y>.  
>

---
