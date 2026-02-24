# #534 - Boost release notes claim C++ 98 compatibility, but do not state that bootstrapping build requires a C++ 11 compiler [Closed]

> Username: ned14  
> Created at: 2019-12-19 16:46:19 UTC  
> Updated at: 2020-12-22 20:49:42 UTC  
> Closed at: 2020-12-22 20:49:41 UTC  
> Url: https://github.com/boostorg/build/issues/534  

See https://github.com/boostorg/build/issues/519 closed.  
  
The release notes need to state this in my opinion. It is not obvious, and was not the case until very recently.

---

## Comment 1

> Username: glenfe  
> Created at: 2020-01-03 16:11:28 UTC  
> Url: https://github.com/boostorg/build/issues/534#issuecomment-570617899  

None of the Boost Build developers made any attempt to communicate this change to Boost users, and the Boost release process automatically picks up the latest Boost Build. Maybe the problem is the latter.

---

## Comment 2

> Username: tanzislam  
> Created at: 2020-02-02 15:35:12 UTC  
> Url: https://github.com/boostorg/build/issues/534#issuecomment-581146850  

There is also https://github.com/boostorg/build/issues/473 for this.

---

## Comment 3

> Username: Volvox55  
> Created at: 2020-02-19 11:42:16 UTC  
> Url: https://github.com/boostorg/build/issues/534#issuecomment-588180549  

#535 is also the same issue

---

## Comment 4

> Username: grafikrobot  
> Created at: 2020-12-22 20:49:41 UTC  
> Url: https://github.com/boostorg/build/issues/534#issuecomment-749769183  

We are now using more C++11 features, and more to come. It's not tractable to support ongoing b2 engine development below C++11.
