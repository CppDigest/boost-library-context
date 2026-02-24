# #1290 - Use of deprecated ASIO interfaces [Closed]

> Username: djarek  
> Created at: 2018-11-08 22:43:10 UTC  
> Updated at: 2018-11-27 00:12:42 UTC  
> Closed at: 2018-11-27 00:12:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1290  

Some tests fails to compile because of use of depracated entities in ASIO, compile with `cxxflags="-DBOOST_ASIO_NO_DEPRECATED"` to reproduce.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-11-08 22:44:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1290#issuecomment-437183509  

https://github.com/boostorg/beast/blob/develop/Jamfile#L89
