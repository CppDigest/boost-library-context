# #18  Restore change to eliminate warning from GCC 4.4.7 and newer [Closed]

> Username: saturnjct  
> Created at: 2016-03-28 19:51:33 UTC  
> Updated at: 2016-04-01 19:49:52 UTC  
> Closed at: 2016-04-01 19:49:40 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/18  

Restoring version of file from bdc3557 to eliminate warnings from GCC 4.4.7, GCC 4.9.2 and possibly others. Original patch provided by https://github.com/plopresti

---

## Comment 1

> Username: apolukhin  
> Created_at: 2016-03-29 06:27:44 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/18#issuecomment-202738478  

This will cause regression for move-only types https://svn.boost.org/trac/boost/ticket/11570  
  
I'll appreciate some other solution to suppress warnings.

---

## Comment 2

> Username: plopresti  
> Created_at: 2016-04-01 17:11:16 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/18#issuecomment-204474439  

A simple `Target result = Target();` as initializer should work, I think. It only requires that the type be move-constructible in C++11 and above.  
  
There are known compiler bugs actually performing the initialization, but since the point here is simply to silence warnings on particular compilers anyway, that should not matter.  
  
It's a trivial patch, but I will set up the pull request anyway.

---

## Comment 3

> Username: plopresti  
> Created_at: 2016-04-01 17:36:55 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/18#issuecomment-204483402  

New pull request created (https://github.com/boostorg/lexical_cast/pull/19).

---

## Comment 4

> Username: plopresti  
> Created_at: 2016-04-01 18:04:16 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/18#issuecomment-204496317  

Oh, one more thing...  As the Trac ticket suggests, the original patch can be fixed up with some "std::move magic" like so:  
  
`#include <boost/move/move.hpp>`  
...  
`return boost::move(get(result));`  
  
(Using boost::move keeps it all working with older compilers.)  
  
But this is an awful lot of machinery just to silence a warning... I think the simple initialization fix is better.

---

## Comment 5

> Username: apolukhin  
> Created_at: 2016-04-01 19:49:40 UTC  
> Updated_at: 2016-04-01 19:49:52 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/18#issuecomment-204541378  

Thanks, merged the #19

---
