# #34 - utf8_codecvt.hpp has CRLF line terminators [Closed]

> Username: kivadiu  
> Created at: 2018-06-18 07:04:47 UTC  
> Updated at: 2019-12-13 18:25:40 UTC  
> Closed at: 2019-12-13 18:25:40 UTC  
> Url: https://github.com/boostorg/nowide/issues/34  

This file is the only one that has CRLF line terminators. Other have only LF (unix). Why?  
Could all the files have the same line ending?

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2018-06-18 07:07:48 UTC  
> Url: https://github.com/boostorg/nowide/issues/34#issuecomment-397961082  

Ok I'll check

---

## Comment 2

> Username: kivadiu  
> Created at: 2018-06-18 09:21:47 UTC  
> Url: https://github.com/boostorg/nowide/issues/34#issuecomment-397993740  

Fixed here: https://github.com/artyom-beilis/nowide/pull/38

---

## Comment 3

> Username: Flamefire  
> Created at: 2019-12-13 18:25:39 UTC  
> Url: https://github.com/boostorg/nowide/issues/34#issuecomment-565553384  

Fixed by 98cda02 and 668a526
