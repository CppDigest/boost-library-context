# #19 - Build with exceptions disabled is not supported [Closed]

> Username: ivaigult  
> Created at: 2017-07-17 14:52:16 UTC  
> Updated at: 2023-03-04 16:13:53 UTC  
> Closed at: 2023-03-04 16:13:53 UTC  
> Url: https://github.com/boostorg/nowide/issues/19  

Not really an issue, but feature request.   
  
I reviewed library source code and it doesn't seem to rely on exceptions heavily. It just need replacement from `try` to `BOOST_TRY`,  `catch` to  `BOOST_CATCH` and so on. Also , `throw()` or `nothrow` need to be replaced to appropriated macros.

---

## Comment 1

> Username: Flamefire  
> Created at: 2019-12-13 18:52:42 UTC  
> Url: https://github.com/boostorg/nowide/issues/19#issuecomment-565563330  

Do you have a real use case for this?

---

## Comment 2

> Username: Flamefire  
> Created at: 2023-03-04 16:13:53 UTC  
> Url: https://github.com/boostorg/nowide/issues/19#issuecomment-1454790181  

Closing this old feature request due to inactivity
