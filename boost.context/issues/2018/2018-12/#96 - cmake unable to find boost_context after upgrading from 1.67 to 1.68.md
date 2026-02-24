# #96 - cmake unable to find boost_context after upgrading from 1.67 to 1.68 [Closed]

> Username: jdmairs  
> Created at: 2018-12-22 19:12:21 UTC  
> Updated at: 2018-12-23 20:24:47 UTC  
> Closed at: 2018-12-23 20:24:47 UTC  
> Url: https://github.com/boostorg/context/issues/96  

So for a while now I use cmake and find_boost to find the headers and libs that I then link to.  CMake build worked great no problems.  I upgraded to 1.68 and it finds all the other boost libs (system, thread, regex, etc) but boost_context or the context lib.    
  
The library is built and sitting comfortably in stage/lib.  I'm using gcc 5.5 and cmake 3.11.4.  
  
Any ideas?  
  
Should I have tried stack overflow first?

---

## Comment 1

> Username: olk  
> Created at: 2018-12-22 22:14:47 UTC  
> Url: https://github.com/boostorg/context/issues/96#issuecomment-449600745  

AFAIK, boost doesn't support cmake (yet)

---

## Comment 2

> Username: jdmairs  
> Created at: 2018-12-23 14:51:06 UTC  
> Url: https://github.com/boostorg/context/issues/96#issuecomment-449641262  

To clarify I'm not talking about building boost with cmake...obviously one must still use b2.  I'm talking about building my own project with cmake.  Cmake has supported finding boost for a while and as I said it found boost 1.67 libs with no problem.

---

## Comment 3

> Username: olk  
> Created at: 2018-12-23 15:48:38 UTC  
> Url: https://github.com/boostorg/context/issues/96#issuecomment-449644836  

I don't use cmake - I guess your problem is related to the removed file boost/context/all.hpp. In order to detect boost.context, cmake should use boost/context/detail/fcontext.hpp instead.

---

## Comment 4

> Username: jdmairs  
> Created at: 2018-12-23 18:52:32 UTC  
> Url: https://github.com/boostorg/context/issues/96#issuecomment-449656626  

I think you may be on to something.  I'll post this in the cmake forum.

---

## Comment 5

> Username: jdmairs  
> Created at: 2018-12-23 18:52:50 UTC  
> Url: https://github.com/boostorg/context/issues/96#issuecomment-449656644  

Thank you for looking into the details.
