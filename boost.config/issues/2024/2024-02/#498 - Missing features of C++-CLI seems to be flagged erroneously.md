# #498 - Missing features of C++/CLI seems to be flagged erroneously [Open]

> Username: WebDancer69  
> Created at: 2024-02-15 16:28:15 UTC  
> Updated at: 2024-06-17 13:48:06 UTC  
> Url: https://github.com/boostorg/config/issues/498  

We are using boost with C++/CLI, but some of the limitations does not seem to be valid.  
  
The config visualc.hpp conditionally sets these:  
BOOST_NO_CXX11_THREAD_LOCAL  
BOOST_NO_SFINAE_EXPR  
BOOST_NO_CXX11_REF_QUALIFIERS  
BOOST_NO_CXX11_CONSTEXPR  
  
But given the updates to C++/CLI, in particular the recent ones introducing C++20, I don't believe all of these limitations are currently the case.  
  
Also, just missing one of these triggers "does not support C++11", which then invalidates big parts boost for C++/CLI, and soon probably entire boost, unless this is fixed.  
  
Are there some test we can run to verify what is required, and potentially remove (some of) these limitations?

---

## Comment 1

> Username: martinfehrs  
> Created at: 2024-06-17 08:14:44 UTC  
> Url: https://github.com/boostorg/config/issues/498#issuecomment-2173444774  

Same problem here. We are using boost and C++/CLI. Newer versions do not work anymore, because of some limitations of C++/CLI, even if these limitations are irrelevant for the particular boost library.
