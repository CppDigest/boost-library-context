# #204 - *_url constructors can also throw std::length_error [Closed]

> Username: alandefreitas  
> Created at: 2022-06-17 16:50:40 UTC  
> Updated at: 2022-09-15 19:48:45 UTC  
> Closed at: 2022-09-15 19:48:45 UTC  
> Url: https://github.com/boostorg/url/issues/204  

This needs to be documented.  
  
https://github.com/CPPAlliance/url/issues/203#issuecomment-1158852601

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-23 22:26:46 UTC  
> Url: https://github.com/boostorg/url/issues/204#issuecomment-1224952574  

I might have already done this, switched it to `std::exception` because now the throw comes from `result::value()`.
