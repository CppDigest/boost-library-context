# #56 Better support for null requests [Merged]

> Username: aminiussi  
> Created at: 2018-03-13 17:21:06 UTC  
> Updated at: 2018-05-21 12:58:56 UTC  
> Merged at: 2018-05-21 12:58:56 UTC  
> Closed at: 2018-05-21 12:58:56 UTC  
> Url: https://github.com/boostorg/mpi/pull/56  

Right now,our current support for null request is sloppy. We have no easy way to tell if a possibly composite request is null.  
  
This raise a few worrying situations. For example, our wait_any is easily defeated if we mix composite and simple requests where MPI_Waitany is not. Also, our wait_any does not support a sequence of null request where its C counterpart does.

---

## Comment 1

> Username: Rombur  
> Created_at: 2018-04-09 13:19:22 UTC  
> Url: https://github.com/boostorg/mpi/pull/56#issuecomment-379748807  

Is there a problem with github on this PR? I only see one commit about boost.mpi and nothing about null request...

---

## Comment 2

> Username: aminiussi  
> Created_at: 2018-04-09 13:21:45 UTC  
> Url: https://github.com/boostorg/mpi/pull/56#issuecomment-379749507  

Yes there is, maybe I'am a few pushes late.  
Looking into it.

---

## Comment 3

> Username: aminiussi  
> Created_at: 2018-04-09 14:06:14 UTC  
> Url: https://github.com/boostorg/mpi/pull/56#issuecomment-379764160  

Sorry, I got lost in my branches, this one is actually work in progress (yet to be). I created it because I cannot create issues and need to keep track of the work somehow.   
I got mixed up with #53 (which triggered this issue regarding the way we deal with null request, but is actually merged).  
   
I uses both gitlab and github API and sometime get lost in the small differences (this would be tagged WIP in gitlab).  
  
Sorry for the inconvenience.

---
