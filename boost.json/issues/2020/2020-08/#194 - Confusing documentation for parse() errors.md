# #194 - Confusing documentation for parse() errors [Closed]

> Username: Eelis  
> Created at: 2020-08-23 19:24:30 UTC  
> Updated at: 2020-08-23 22:23:46 UTC  
> Closed at: 2020-08-23 22:23:46 UTC  
> Url: https://github.com/boostorg/json/issues/194  

On https://vinniefalco.github.io/doc/json/json/ref/boost__json__parse/overload2.html , the "Exceptions" section says that `system_error` is thrown on failure, but the "Description" and "Return Value" sections say that null will be returned if an error occurs. If an exception is thrown, there is no return value, right?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-08-23 19:28:30 UTC  
> Url: https://github.com/boostorg/json/issues/194#issuecomment-678814503  

yeah yeah...
