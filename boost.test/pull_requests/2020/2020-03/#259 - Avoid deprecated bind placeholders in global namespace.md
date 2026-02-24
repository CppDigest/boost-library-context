# #259 Avoid deprecated bind placeholders in global namespace [Merged]

> Username: mloskot  
> Created at: 2020-03-07 22:29:49 UTC  
> Updated at: 2020-04-21 14:23:41 UTC  
> Merged at: 2020-03-29 14:53:42 UTC  
> Closed at: 2020-03-29 14:53:42 UTC  
> Url: https://github.com/boostorg/test/pull/259  

This fixes numerous yet annoying compilation warnings:  
  
```  
  note: #pragma message:  
    The practice of declaring the Bind placeholders (_1, _2, ...)  
    in the global namespace is deprecated.  
    Please use <boost/bind/bind.hpp> + using namespace boost::placeholders,  
    or define BOOST_BIND_GLOBAL_PLACEHOLDERS to retain the current behavior.  
```  
  
Apart from obscuring the output of compilation of libraries using Boost.Test, it may be troublesome on CI builds. On many CI services every line of log counts and if log dumps become too long,   
builds are terminated.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2020-03-29 16:29:14 UTC  
> Url: https://github.com/boostorg/test/pull/259#issuecomment-605662791  

In master

---

## Comment 2

> Username: mloskot  
> Created_at: 2020-03-29 17:53:28 UTC  
> Url: https://github.com/boostorg/test/pull/259#issuecomment-605674503  

Thank you

---
