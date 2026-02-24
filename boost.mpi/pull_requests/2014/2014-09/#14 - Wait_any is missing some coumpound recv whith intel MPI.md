# #14 Wait_any is missing some coumpound recv whith intel MPI [Merged]

> Username: aminiussi  
> Created at: 2014-09-10 11:45:21 UTC  
> Updated at: 2014-09-16 12:08:45 UTC  
> Merged at: 2014-09-16 12:08:44 UTC  
> Closed at: 2014-09-16 12:08:44 UTC  
> Url: https://github.com/boostorg/mpi/pull/14  

In wait_any, we need to test for compound recv of serialized object that just got the size but not the body (that is, request[1] == null & handler != null).  
  
Note that in:  
  
if (current->m_requests[0] != MPI_REQUEST_NULL &&  
        (current->m_requests[1] != MPI_REQUEST_NULL ||  
         current->m_handler)) {  
      if (optional<status> result = current->test())  
        return std::make_pair(*result, current);  
    }  
  
I suspect the 3 line test should be removed and every thng handled in request::test (which is supposed to tell wheter we have a complete request).  
But I have no idea why it was done like that in the first place.

---
