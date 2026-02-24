# #152 - How to build boost_stacktrace_from_exception [Closed]

> Username: meastp  
> Created at: 2024-02-14 22:11:39 UTC  
> Updated at: 2024-02-19 08:08:18 UTC  
> Closed at: 2024-02-19 08:08:18 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/152  

The library `boost_stacktrace_from_exception` described in the documentation[1] isn't built by default, it seems, and I can't find any documentation about it (almost no results from google).  
  
How do I build it?  
  
Do I have to use dynamic or static linking?  
  
[1] https://www.boost.org/doc/libs/master/doc/html/stacktrace/getting_started.html#stacktrace.getting_started.stacktrace_from_arbitrary_except

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-02-15 07:42:48 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/152#issuecomment-1945517789  

What version of boost are you using?

---

## Comment 2

> Username: apolukhin  
> Created at: 2024-02-15 16:18:02 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/152#issuecomment-1946460070  

The `boost_stacktrace_from_exception` was not released yet, you should take the develop version of the Boost.Stacktrace library to use it. The `boost_stacktrace_from_exception` would first appear in Boost 1.85

---

## Comment 3

> Username: meastp  
> Created at: 2024-02-16 10:14:01 UTC  
> Updated at: 2024-02-16 10:14:09 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/152#issuecomment-1948099741  

@apolukhin Aaah, thank you - it didn't occour to me that I was looking at the dev documentation. I'm really looking forward to this feature, then - it will be very, very useful.  
  
I saw multiple (two?) iso c++ papers on variants of this feature - is there any progress or were both/all papers stopped when ewg evaluated them?

---

## Comment 4

> Username: apolukhin  
> Created at: 2024-02-17 09:17:38 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/152#issuecomment-1949912039  

There's no progress in this feature standardization as the feedback on the feature was mixed.  
  
So I'm planning to provide the functionality in Boost, gather feedback and gain more experience in the topic. Meanwhile people will get used to the feature. After that I hope that the the discussion in WG21 would go more smooth.
