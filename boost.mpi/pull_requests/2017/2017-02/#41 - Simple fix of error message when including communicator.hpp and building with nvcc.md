# #41 Simple fix of error message when including communicator.hpp and building with nvcc [Closed]

> Username: pkestene  
> Created at: 2017-02-14 14:43:18 UTC  
> Updated at: 2017-02-23 12:58:49 UTC  
> Closed at: 2017-02-23 12:58:49 UTC  
> Url: https://github.com/boostorg/mpi/pull/41  

Ticket #10998 (https://svn.boost.org/trac/boost/ticket/10998) reported a problem which prevents  
building any boost::mpi application with compiler nvcc / icc  
  
The error message is :  
  
boost/mpi/communicator.hpp(1750): error: explicit specialization of function "boost::mpi::communicator::send(int, int, const T &) const [with T=boost::mpi::packed_oarchive]" must precede its first use (  
(1177): here)  
  
  
Here is the dummy solution (moving code as the ticket reporter said)  
  
There is probably a better solution.

---

## Comment 1

> Username: gnthibault  
> Created_at: 2017-02-21 20:55:09 UTC  
> Updated_at: 2017-02-22 08:22:55 UTC  
> Url: https://github.com/boostorg/mpi/pull/41#issuecomment-281477181  

Thank you pkestene, I hope reviewer will integrate your pull request because this aspect is critical for using boost::mpi in conjunction with nvcc for cuda applications.

---

## Comment 2

> Username: aminiussi  
> Created_at: 2017-02-23 10:20:35 UTC  
> Url: https://github.com/boostorg/mpi/pull/41#issuecomment-281953110  

Could you please try to rebase on develop branch ?  
I'm having some compilation issue which I will investigate but it could be faster for you.  
Thanks

---

## Comment 3

> Username: aminiussi  
> Created_at: 2017-02-23 10:47:51 UTC  
> Url: https://github.com/boostorg/mpi/pull/41#issuecomment-281959354  

By the way, I think the compiler is incorrect, what matter is that the _point of instantiation_ for template at line 1177 must be placed after the specialization.  
  
Still, since the issue is with partial specialization declaration (definitions are in communicator.cpp), it could be more elegant to put them just after the class definition.  
I'm gonna try that (I do not have easy access to the specified platform but the error message should disappear).  
  
Thanks

---

## Comment 4

> Username: aminiussi  
> Created_at: 2017-02-23 12:58:45 UTC  
> Url: https://github.com/boostorg/mpi/pull/41#issuecomment-281985155  

Fixed in #45 thanks a lot and sorry for the delay.

---
