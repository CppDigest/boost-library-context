# #424 X3: Use constexpr in numeric utils instead of static storage [Merged]

> Username: Kojoley  
> Created at: 2018-11-23 14:29:18 UTC  
> Updated at: 2018-11-24 14:53:58 UTC  
> Merged at: 2018-11-24 14:53:51 UTC  
> Closed at: 2018-11-24 14:53:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/424  

It is a symmetrical change to Qi one that landed a while ago d8637825 (#146).  
There should not be race condition on C++11+, but MSVC still does not do compile-time initialization of static variables when constructor is constexpr https://developercommunity.visualstudio.com/content/problem/336946/class-with-constexpr-constructor-not-using-static.html

---
