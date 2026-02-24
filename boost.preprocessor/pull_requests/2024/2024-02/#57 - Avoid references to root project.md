# #57 Avoid references to root project. [Merged]

> Username: grafikrobot  
> Created at: 2024-02-04 15:58:57 UTC  
> Updated at: 2024-05-07 21:38:11 UTC  
> Merged at: 2024-02-04 17:29:24 UTC  
> Closed at: 2024-02-04 17:29:24 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/57  

In order to make the library transportable, specifically so we can support modular Boost distribution, we need to avoid encoding absolute paths to the monolithic Boost structure. This changes the tests to refer only to the local include path.  
  
See https://lists.boost.org/Archives/boost/2024/01/255704.php

---
