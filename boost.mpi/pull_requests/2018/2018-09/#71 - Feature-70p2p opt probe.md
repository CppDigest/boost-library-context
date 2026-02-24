# #71 Feature/70p2p opt probe [Merged]

> Username: aminiussi  
> Created at: 2018-09-10 08:59:40 UTC  
> Updated at: 2018-12-09 22:17:33 UTC  
> Merged at: 2018-12-09 22:17:33 UTC  
> Closed at: 2018-12-09 22:17:33 UTC  
> Url: https://github.com/boostorg/mpi/pull/71  

This pull request was initially triggered by #63 and #70 and concerns point to point communication involving variable size variable size messages (serialized data, arrays of serialized data, and dynamic array of primitive data).  
  
It basically an optimization. Instead of sending first the size of the message and then the payload, we directly send the payload and uses a call to of of MPI_[M][I]Probe and the receiver side to allocated the receiving buffer.  
It fixes the **mpi_any_source** problem mentioned in #63 since we only have one message.   
  
Note that it won't work with Intel MPI 2018 and older due to a problem in the probe function. So we fall back on the "old" communication in that case.

---

## Comment 1

> Username: aminiussi  
> Created_at: 2018-09-10 09:05:28 UTC  
> Url: https://github.com/boostorg/mpi/pull/71#issuecomment-419841965  

@hirschsn I can't add you to the reviewer list, does this fix your problem ?

---

## Comment 2

> Username: aminiussi  
> Created_at: 2018-09-11 08:03:09 UTC  
> Url: https://github.com/boostorg/mpi/pull/71#issuecomment-420184484  

I'd like to merge that soon (by end of the week) so that it can spend as much time as possible in the tests before the next release.

---

## Comment 3

> Username: Belcourt  
> Created_at: 2018-09-11 14:09:25 UTC  
> Url: https://github.com/boostorg/mpi/pull/71#issuecomment-420287785  

This appears to be a huge commit.  I can try to review it this weekend, would that work?  Is it possible to reduce the size to something smaller, that would increase the probability of a review?

---

## Comment 4

> Username: aminiussi  
> Created_at: 2018-09-11 15:54:38 UTC  
> Url: https://github.com/boostorg/mpi/pull/71#issuecomment-420324025  

This week end would be ok.  
  
I don't think reducing the size is possible, or let's easy enough to be done incrementally by me (I had to maintain the regression testing ok along the way to keep it manageable). Actually, some stuff in the request interface could be improved, but was left out for future work as it would impact non all the wait_* ans test_* code. (deleting the letter/handler once the request is performed, might improve performances, but would require some more changes and thinking w.r.t. request API. So it might be better better left for later too).  
  
The problem is that, due to the intel bug, we need to be able to switch between the non MPI_Probe implementation that use two MPI messages (for size and payload) and the new one (no size message). And the MPI standard now say that we need to be able to mix blocking and non blocking send/recv, so we need to keep all that stuff in sync. That adds a lot of modifications.   
  
Also, in order to get some flexibility while maintaining backward compatibility (including the possibility to select the implementation at run time, which might be a useful feature for user with "exotic" MPI implementations), I had to implement requests, including the legacy behavior, with the envelop/letter idiom (or kept all the legacy request fields for no reason, which is difficult to justify as requests are used as light weight objects).  
  
And then there was a chicken eggs problem with include files which implied splitting a header (some improvement could be done in this area, but that can be left for later).  
  
One alternative would have been to first provide a "white shot" implementation that would do the same thing but with the letter/envelope, and then add the new request implementation, but due to the above constraints, I'm not sure it would have been that easier (actually, a similar branch spent more than a year waiting for merge and had to be removed eventually, but the changes were not that smaller either).  
  
There are basically two main layer of modifications:  
  
 1. move the request to the letter/envelope idiom. The envelope is the request, which remains a lightweight object, or glorified smart pointer, and the letter is the actual request handler, which derives from "request::handler". Although it does not introduce observable modifications, it's probably the bigger change. I removed the intermediate static handler functions since, although virtual method already implies an indirect, their body can directly do their job with no further indirection. It's big, but mostly a reorganization (these are the "legacy" familly of request handler).  
 2. add the new MPI_[M][I]Probe request handler implementation (each request::legacy_XXX class has a functionally equivalent XXX handler based on probes) along this the selection between the tzo implementations.  
  
But yes, it's a big change. Basically the original corner case bug related to any_source is accidentally fixed through a global optimization rewrite (one less message in point to point serialized object transfert).

---

## Comment 5

> Username: bmario  
> Created_at: 2018-09-12 11:47:20 UTC  
> Url: https://github.com/boostorg/mpi/pull/71#issuecomment-420618949  

This seems related to the issue @tilsche had reported here:  
  
https://svn.boost.org/trac10/ticket/12828#no1

---

## Comment 6

> Username: aminiussi  
> Created_at: 2018-09-13 07:17:28 UTC  
> Url: https://github.com/boostorg/mpi/pull/71#issuecomment-420908388  

> This seems related to the issue @tilsche had reported here:  
>   
> https://svn.boost.org/trac10/ticket/12828#no1  
  
It seems to be fixed in that branch, but I don't have an account on the svn tracker.

---

## Comment 7

> Username: tilsche  
> Created_at: 2018-09-20 12:36:59 UTC  
> Url: https://github.com/boostorg/mpi/pull/71#issuecomment-423166841  

It looks like this change could in fact fix the linked crash. Thanks for taking a shot at the boost.MPI implementation!

---

## Comment 8

> Username: hirschsn  
> Created_at: 2018-10-08 09:24:24 UTC  
> Url: https://github.com/boostorg/mpi/pull/71#issuecomment-427770435  

Sorry that it took me so long to react.  
This looks very good and indeed solves the `any_source` problem.  
Thank your for taking the time to rework the code.

---

## Comment 9

> Username: aminiussi  
> Created_at: 2018-10-08 09:32:34 UTC  
> Updated_at: 2018-10-08 09:32:55 UTC  
> Url: https://github.com/boostorg/mpi/pull/71#issuecomment-427772628  

Thanks, I was away for some time.  
  
So, if I'm not overlooking anything, I need to:  
 1. Check documentation and explain what to expect, especially regarding communication progress.  
 2. Check automatic feature activation (on by default except for Intel up to 18 included)  
  
regarding 2, my IT is working on the installation of intel19 in order to check that their MPI_Probe bug is fixed on that version.  
  
Thanks!

---

## Comment 10

> Username: hirschsn  
> Created_at: 2018-10-08 09:38:51 UTC  
> Updated_at: 2018-10-08 09:39:04 UTC  
> Url: https://github.com/boostorg/mpi/pull/71#issuecomment-427774233  

Regarding 1: Documenting some internals ("communication progress") is indeed a good idea. With regard to #72: Maybe a (prominent) section about incompatibilities with the MPI standard could be added to the documentation. (With "prominent" I mean somewhere in the front such that a user might actually be tempted to read it.)

---
