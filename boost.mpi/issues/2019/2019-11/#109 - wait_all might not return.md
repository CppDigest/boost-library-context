# #109 - wait_all might not return [Closed]

> Username: joprodrigues  
> Created at: 2019-11-10 21:03:49 UTC  
> Updated at: 2020-06-22 15:58:04 UTC  
> Closed at: 2020-06-22 15:58:04 UTC  
> Url: https://github.com/boostorg/mpi/issues/109  

When using MPI Boost 1.71, the wait_all function may not return, if some of the isends have already been completed (I think).  
  
What I'm doing is roughly this:  
I have a producer that sends workunits to multiple consumers, using isend.  
When the producer is through, it sends a last message to the consumers that the data is over.  
To clean up the isends, I'm doing a wait_all to the vector where the isend requests are stored, to ensure all the requests have been completed (I think it should be a no-op since I have already receive all the data from the consumers).  
  
As a workaround, I've written this (which returns, has expected):  
for(auto current = pending_isends.begin(); current != current_isends.end(); ++current){  
    if (!current->active()){  
        current->wait();  
    }  
}

---

## Comment 1

> Username: aminiussi  
> Created at: 2019-11-11 00:20:14 UTC  
> Url: https://github.com/boostorg/mpi/issues/109#issuecomment-552251270  

Hi,  
  
I will need a minimal test case and the underlying MPI implementation to reproduce the problem. If the isend are completed, I'm not sure why we should not detect it through the underlying implementation.  
  
As such, I really cannot tell what is/could be the problem as there are a lot of possible variations based on:  
 * does underlying MPI support mpi probe ?  
 * what are the tags ?  
 * is the transmitted data type primitive ?  
  
Thanks

---

## Comment 2

> Username: joprodrigues  
> Created at: 2019-11-23 12:27:46 UTC  
> Url: https://github.com/boostorg/mpi/issues/109#issuecomment-557793819  

Hi,  
I have a test case extracted from my program here: https://gist.github.com/joprodrigues/0972efd1b5902a22eb9fd26e14f5836a  
  
I can work on a smaller test case, if you want.  
  
I'm using OpenMPI 3 (3.1.3-11) from Debian 10.  
  
Thank you for your interest

---

## Comment 3

> Username: aminiussi  
> Created at: 2019-11-24 18:07:48 UTC  
> Url: https://github.com/boostorg/mpi/issues/109#issuecomment-557912872  

Well, the smaller the better :-)  
I'll try to reproduce as such already.

---

## Comment 4

> Username: aminiussi  
> Created at: 2020-06-22 15:58:03 UTC  
> Url: https://github.com/boostorg/mpi/issues/109#issuecomment-647613903  

Hi, it looks like @hirschsn fixed it in #117
