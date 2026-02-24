# #572 - Add comments to ci_equal [Closed]

> Username: alandefreitas  
> Created at: 2022-09-27 21:26:17 UTC  
> Updated at: 2022-10-12 18:23:17 UTC  
> Closed at: 2022-10-12 18:23:17 UTC  
> Url: https://github.com/boostorg/url/issues/572  

Add comments about the expectation of forward iterators in ci_equal.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-28 16:10:19 UTC  
> Url: https://github.com/boostorg/url/issues/572#issuecomment-1261137225  

And explore the possibility of using `size()` somehow

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-09-28 17:26:23 UTC  
> Updated at: 2022-09-28 17:26:46 UTC  
> Url: https://github.com/boostorg/url/issues/572#issuecomment-1261229724  

> And explore the possibility of using size() somehow  
  
I thought you didn't want that anymore. But OK. So, to be clear:  
  
- We keep the overload string_view overload  
- Make two versions of the template depending on whether `size()` exists?  
  
?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-09-28 18:00:47 UTC  
> Url: https://github.com/boostorg/url/issues/572#issuecomment-1261281902  

"explore" means we are doing design work not coding. Probably the best way to start is to ask Peter. The issue is that there is no standard concept of "range with size()" in C+11.

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-09-28 18:39:33 UTC  
> Url: https://github.com/boostorg/url/issues/572#issuecomment-1261320791  

True: https://en.cppreference.com/w/cpp/ranges/range  
  
The best we have now is https://en.cppreference.com/w/cpp/ranges/size
