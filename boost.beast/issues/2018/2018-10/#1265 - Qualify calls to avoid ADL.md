# #1265 - Qualify calls to avoid ADL [Closed]

> Username: vinniefalco  
> Created at: 2018-10-10 14:34:06 UTC  
> Updated at: 2022-06-16 16:25:13 UTC  
> Closed at: 2022-06-16 16:25:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1265  

When the implementation calls internal functions which accept template types, the call should be made with namespace qualification, e.g. `beast::` or `beast::detail::` to prevent ADL from taking effect where it is unwanted.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2018-11-27 01:09:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1265#issuecomment-441859445  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-06-16 16:25:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1265#issuecomment-1157875231  

We have done better with this, but we should wait until there is a specific problem to know which ones to fix.
