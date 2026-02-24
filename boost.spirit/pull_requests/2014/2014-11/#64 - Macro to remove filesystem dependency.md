# #64 Macro to remove filesystem dependency [Merged]

> Username: wichtounet  
> Created at: 2014-11-01 13:39:39 UTC  
> Updated at: 2014-11-03 07:58:17 UTC  
> Merged at: 2014-11-01 19:03:41 UTC  
> Closed at: 2014-11-01 19:03:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/64  

This patch adds the BOOST_SPIRIT_X3_NO_FILESYSTEM macro which removes the dependency on boost filesystem when enabled.   
  
There is only one line in X3 that depends on filesystem, so I think that it is a good feature to let the user disable it if it doesn't want the dependency.

---
