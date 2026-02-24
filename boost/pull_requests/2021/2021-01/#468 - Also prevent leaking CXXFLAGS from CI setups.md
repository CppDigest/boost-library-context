# #468 Also prevent leaking CXXFLAGS from CI setups. [Merged]

> Username: grafikrobot  
> Created at: 2021-01-21 16:53:22 UTC  
> Updated at: 2021-01-22 15:52:11 UTC  
> Merged at: 2021-01-22 15:26:07 UTC  
> Closed at: 2021-01-22 15:26:07 UTC  
> Url: https://github.com/boostorg/boost/pull/468  

Looks like CXXFLAGS is also magically set in some CI setups to incompatible values to the toolset b2 uses for bootstrap. This clears it to avoid the confusion.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2021-01-22 13:45:48 UTC  
> Url: https://github.com/boostorg/boost/pull/468#issuecomment-765406909  

@pdimov or @glenfe merge please.

---
