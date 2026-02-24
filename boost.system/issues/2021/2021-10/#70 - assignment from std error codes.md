# #70 - assignment from std error codes [Closed]

> Username: vinniefalco  
> Created at: 2021-10-10 22:26:54 UTC  
> Updated at: 2021-10-11 04:37:56 UTC  
> Closed at: 2021-10-11 04:37:56 UTC  
> Url: https://github.com/boostorg/system/issues/70  

Can this be made to work without too much contortion?  
```  
boost::system::error_code ec = std::io_errc::stream;  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2021-10-10 22:50:36 UTC  
> Url: https://github.com/boostorg/system/issues/70#issuecomment-939566192  

Turns out, it already works on MSVC, but not on "real" compilers. :-)

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-10-11 01:36:26 UTC  
> Url: https://github.com/boostorg/system/issues/70#issuecomment-939606455  

LOL

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-10-11 01:36:41 UTC  
> Url: https://github.com/boostorg/system/issues/70#issuecomment-939606562  

broken clocks right at least twice a day and all that
