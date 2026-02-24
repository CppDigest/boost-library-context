# #87 Fix compilation with BOOST_NO_EXCEPTIONS [Closed]

> Username: MarcelRaad  
> Created at: 2015-09-18 10:46:45 UTC  
> Updated at: 2015-09-18 19:42:32 UTC  
> Closed at: 2015-09-18 15:45:44 UTC  
> Url: https://github.com/boostorg/test/pull/87  

The return type of throw_exception was missing.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2015-09-18 11:23:37 UTC  
> Url: https://github.com/boostorg/test/pull/87#issuecomment-141422518  

Thanks,  
  
is it `inline void BOOST_NO_RETURN` or `BOOST_NO_RETURN inline void` ?

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2015-09-18 11:35:58 UTC  
> Url: https://github.com/boostorg/test/pull/87#issuecomment-141424272  

With both MSVC14 and clang-cl, both versions work.

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2015-09-18 15:45:39 UTC  
> Url: https://github.com/boostorg/test/pull/87#issuecomment-141486932  

Applied, thanks.  
  
I changed a bit to be in accordance with the documentation I found:  
- http://www.open-std.org/jtc1/sc22/wg14/www/docs/n1453.htm  
- https://msdn.microsoft.com/en-us/library/k6ktzx3s.aspx

---

## Comment 4

> Username: MarcelRaad  
> Created_at: 2015-09-18 19:42:32 UTC  
> Url: https://github.com/boostorg/test/pull/87#issuecomment-141548954  

Great, thanks!

---
