# #53 replace members of std::allocate which are deprecated in c++17 by the… [Merged]

> Username: DanielaE  
> Created at: 2017-12-18 18:55:03 UTC  
> Updated at: 2017-12-24 08:47:14 UTC  
> Merged at: 2017-12-23 19:11:19 UTC  
> Closed at: 2017-12-23 19:11:19 UTC  
> Url: https://github.com/boostorg/regex/pull/53  

replace members of std::allocate which are deprecated in c++17 by their cousins from std::allocator_traits.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: DanielaE  
> Created_at: 2017-12-21 16:32:50 UTC  
> Url: https://github.com/boostorg/regex/pull/53#issuecomment-353395794  

It looks like travis has some problems ...

---

## Comment 2

> Username: DanielaE  
> Created_at: 2017-12-23 09:00:41 UTC  
> Url: https://github.com/boostorg/regex/pull/53#issuecomment-353715207  

Great, all tests are green except for XCode 6 and 7 where bootstrap.sh fails and no tests are run at all.

---
