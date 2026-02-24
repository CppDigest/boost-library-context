# #84 Fix error when mpi::wait_all is called with data of type std::vector [Merged]

> Username: vincentchabannes  
> Created at: 2019-04-01 12:13:04 UTC  
> Updated at: 2019-06-18 08:42:08 UTC  
> Merged at: 2019-06-11 16:42:26 UTC  
> Closed at: 2019-06-11 16:42:26 UTC  
> Url: https://github.com/boostorg/mpi/pull/84  

Hi boost developers.  
After to have upgraded my boost version to 1.69 and I have this error :  
  
```  
communicator.hpp:1914: static optional<boost::mpi::status> boost::mpi::request::handle_dynamic_primitive_array_irecv(boost::mpi::request *, boost::mpi::request::request_action) [T = int, A = std::allocator<int>]: Assertion `_check_result == MPI_SUCCESS' failed.  
```  
The issue can be reproduce with this code :   
```  
mpi::communicator comm;  
int rank = comm.rank();  
std::vector<int> data;  
std::vector< mpi::request> reqs;  
if ( rank == 0 )  
{  
    for ( int i=0;i<10;++i )  
        data.push_back( i );  
    reqs.push_back( comm.isend(1, 0, data) );  
}  
else if ( rank == 1 )  
{  
    reqs.push_back( comm.irecv(0, 0, data) );  
}  
mpi::wait_all( reqs.begin(), reqs.end() );  
  
if ( rank == 1 )  
{  
     for ( int i=0;i<data.size();++i )  
         std::cout << data[i] << "\n";  
}  
```  
  
It seems that the bug appears from Bugfix/vectors mix 62 #66 .  
  
My fix is just to replace MPI_PACKED by get_mpi_datatype<T>() (only with test() method, it was good with wait() method)

---

## Comment 1

> Username: aminiussi  
> Created_at: 2019-04-19 13:26:30 UTC  
> Url: https://github.com/boostorg/mpi/pull/84#issuecomment-484895760  

Ok, I need to add a test for that one.  
Thanks!

---

## Comment 2

> Username: aminiussi  
> Created_at: 2019-06-11 16:46:48 UTC  
> Url: https://github.com/boostorg/mpi/pull/84#issuecomment-500927834  

@vincentchabannes  Also, I'm assuming you are ok with the license ?

---

## Comment 3

> Username: aminiussi  
> Created_at: 2019-06-11 16:52:16 UTC  
> Url: https://github.com/boostorg/mpi/pull/84#issuecomment-500930094  

@vincentchabannes also, just as a performance issue, sending a vector is two communication process, as vector are not assumed to have a known size (depending on your configuration, it can rely on MPI_Probes which can speed things up).

---

## Comment 4

> Username: vincentchabannes  
> Created_at: 2019-06-18 08:42:08 UTC  
> Url: https://github.com/boostorg/mpi/pull/84#issuecomment-503007889  

thanks @aminiussi.  
And ok with the license.

---
