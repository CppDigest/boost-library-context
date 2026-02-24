# #32 Added macro BOOST_NO_CXX11_FINAL [Merged]

> Username: K-ballo  
> Created at: 2014-08-15 19:13:51 UTC  
> Updated at: 2014-08-19 18:18:26 UTC  
> Merged at: 2014-08-19 17:34:26 UTC  
> Closed at: 2014-08-19 17:34:26 UTC  
> Url: https://github.com/boostorg/config/pull/32  

No changes to compiler config headers as all compilers I have at hand do support it.  
  
A different name might be needed, to leave room to detect support of the _final overrider_.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-08-16 08:03:31 UTC  
> Url: https://github.com/boostorg/config/pull/32#issuecomment-52386985  

Thanks!    
I've just started a discussion on this on the Boost-develop mailing list.  
  
Note that GCC in non C++11 mode will require the macro, as will some early versions - not sure which ones though?

---

## Comment 2

> Username: K-ballo  
> Created_at: 2014-08-16 14:37:14 UTC  
> Url: https://github.com/boostorg/config/pull/32#issuecomment-52394903  

A quick test (online) shows that GCC-4.6.4 does not yet support `final`. I've adjusted the configuration accordingly.  
  
What about every other compiler in non C++11 mode, what is the correct way to handle that?

---

## Comment 3

> Username: K-ballo  
> Created_at: 2014-08-19 17:41:16 UTC  
> Updated_at: 2014-08-19 18:18:26 UTC  
> Url: https://github.com/boostorg/config/pull/32#issuecomment-52670731  

@jzmaddock Shouldn't `BOOST_NO_CXX11_FINAL` be defined for all the other compilers when `__cplusplus < 201103L`?  
  
Nevermind, I see this was taken care of already.

---
