# #64 - hash support [Closed]

> Username: vinniefalco  
> Created at: 2021-10-13 15:35:54 UTC  
> Updated at: 2022-03-16 01:06:51 UTC  
> Closed at: 2022-03-16 01:06:51 UTC  
> Url: https://github.com/boostorg/url/issues/64  

specialize hash for url_view, maybe url and static_url?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-10-13 15:44:13 UTC  
> Url: https://github.com/boostorg/url/issues/64#issuecomment-942435636  

Hash and equality have to match, which may not be trivial if equality uses the RFC algorithm.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-02-22 06:23:50 UTC  
> Url: https://github.com/boostorg/url/issues/64#issuecomment-1047465290  

Related to #65
