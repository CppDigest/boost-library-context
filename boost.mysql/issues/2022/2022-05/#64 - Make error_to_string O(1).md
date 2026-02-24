# #64 - Make error_to_string O(1) [Closed]

> Username: anarthal  
> Created at: 2022-05-11 12:00:55 UTC  
> Updated at: 2023-01-17 21:46:09 UTC  
> Closed at: 2023-01-17 21:46:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/64  



---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-05-11 19:11:12 UTC  
> Url: https://github.com/boostorg/mysql/issues/64#issuecomment-1124192134  

What's wrong with a `switch` statement? Like this:  
https://github.com/boostorg/json/blob/develop/include/boost/json/impl/error.ipp#L31

---

## Comment 2

> Username: anarthal  
> Created at: 2022-05-13 13:15:57 UTC  
> Url: https://github.com/boostorg/mysql/issues/64#issuecomment-1126045673  

Definitely nothing. I generated these files and probably was easier at that point with a `std::find_if`, but should be trivial to rewrite them with a `switch`.
