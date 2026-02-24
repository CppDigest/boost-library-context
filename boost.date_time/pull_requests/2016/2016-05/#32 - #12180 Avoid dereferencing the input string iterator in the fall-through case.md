# #32 #12180 Avoid dereferencing the input string iterator in the fall-through case. [Merged]

> Username: saschazelzer  
> Created at: 2016-05-06 10:20:41 UTC  
> Updated at: 2016-05-09 13:14:51 UTC  
> Merged at: 2016-05-09 13:14:51 UTC  
> Closed at: 2016-05-09 13:14:51 UTC  
> Url: https://github.com/boostorg/date_time/pull/32  

Fixes issue https://svn.boost.org/trac/boost/ticket/12180 by not falling through to the "f" case if the input string does not contain any more characters.

---

## Comment 1

> Username: mclow  
> Created_at: 2016-05-06 15:14:49 UTC  
> Url: https://github.com/boostorg/date_time/pull/32#issuecomment-217470403  

This looks fine to me, but it would be even better if it included a test which failed w/o the patch, and succeeded afterwards.

---

## Comment 2

> Username: saschazelzer  
> Created_at: 2016-05-09 08:36:26 UTC  
> Url: https://github.com/boostorg/date_time/pull/32#issuecomment-217806996  

For this particular problem, I am not sure how to integrate such a test in a meaningful way and I would welcome some guidance here.  
  
The problem was revealed due to the iterator checks done in the debug version of Microsofts STL implementation (using Visual Studio 2013). The release version would just happily dereference the iterator and read whatever value is at that memory location. I do not know how other STL implementations behave in this regard.  
  
I could add a simple test of course, but it would only fail on platforms and build configurations where iterator checking is enabled.

---
