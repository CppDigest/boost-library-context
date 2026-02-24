# #21 Fixed compilation with gcc 4.6 in C++11 mode [Merged]

> Username: Lastique  
> Created at: 2014-07-30 22:32:24 UTC  
> Updated at: 2014-08-04 00:19:52 UTC  
> Merged at: 2014-07-31 05:11:42 UTC  
> Closed at: 2014-07-31 05:11:42 UTC  
> Url: https://github.com/boostorg/thread/pull/21  

The compiler considers invoke() call ambiguous when the function object is actually a function pointer. This caused compilation errors in Boost.Coroutine, which uses invoke() through call_once(). Also added missing includes.  
  
See here for the original compiler error:  
  
http://boost.2283326.n4.nabble.com/1-56-0-Release-candidates-available-tp4665702p4665763.html

---
