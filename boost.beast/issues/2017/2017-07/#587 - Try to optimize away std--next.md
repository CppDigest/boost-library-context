# #587 - Try to optimize away std::next [Closed]

> Username: vinniefalco  
> Created at: 2017-07-04 02:03:22 UTC  
> Updated at: 2017-09-01 03:23:44 UTC  
> Closed at: 2017-09-01 03:23:43 UTC  
> Url: https://github.com/boostorg/beast/issues/587  

A couple of buffer adapters use `std::next` to refresh iterators after copy or move, it shows up in profiles maybe it can use `std::prev` to run in O(1) instead.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-01 03:23:43 UTC  
> Url: https://github.com/boostorg/beast/issues/587#issuecomment-326477731  

Doesn't look like we can do anything
