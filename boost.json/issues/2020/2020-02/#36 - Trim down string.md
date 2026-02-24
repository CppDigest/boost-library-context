# #36 - Trim down string [Closed]

> Username: vinniefalco  
> Created at: 2020-02-01 19:55:52 UTC  
> Updated at: 2020-05-02 01:04:55 UTC  
> Closed at: 2020-05-02 01:04:55 UTC  
> Url: https://github.com/boostorg/json/issues/36  

We can combine separate overloads in `string` into fewer functions accepting a `string_view` parameter. To check if the pointer lies within a range use `std::less`.

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-02-14 17:02:02 UTC  
> Url: https://github.com/boostorg/json/issues/36#issuecomment-586377126  

Addressed by #37
