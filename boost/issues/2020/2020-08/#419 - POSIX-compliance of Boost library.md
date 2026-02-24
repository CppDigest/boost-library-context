# #419 - POSIX-compliance of Boost library [Closed]

> Username: magnusmusngi  
> Created at: 2020-08-08 01:11:01 UTC  
> Updated at: 2020-08-08 05:13:24 UTC  
> Closed at: 2020-08-08 05:13:24 UTC  
> Url: https://github.com/boostorg/boost/issues/419  

I am planning to port a code that uses this Boost library into a POSIX-compliant OS that is neither Linux nor Unix. Given this, I would like to know if this library is POSIX-compliant, and if so, does that mean that I will not have any issues with this library while porting it to the aforementioned OS? Thank you.

---

## Comment 1

> Username: mclow  
> Created at: 2020-08-08 05:13:24 UTC  
> Url: https://github.com/boostorg/boost/issues/419#issuecomment-670825883  

Boost is a collection of over 130 different libraries. Many are POSIX compliant, but some are not.  
A better place to ask your question is on the boost-users mailing list (boost-users@lists.boost.org)
