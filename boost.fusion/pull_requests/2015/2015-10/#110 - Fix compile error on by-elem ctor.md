# #110 Fix compile error on by-elem ctor. [Merged]

> Username: Flast  
> Created at: 2015-10-23 15:14:59 UTC  
> Updated at: 2015-10-24 02:35:15 UTC  
> Merged at: 2015-10-23 23:58:06 UTC  
> Closed at: 2015-10-23 23:58:06 UTC  
> Url: https://github.com/boostorg/fusion/pull/110  

Single element vector of fusion sequence cannot construct from its element type.  
  
This PR will fix failure of [spirit regression](http://www.boost.org/development/tests/develop/developer/spirit.html) tests.

---
