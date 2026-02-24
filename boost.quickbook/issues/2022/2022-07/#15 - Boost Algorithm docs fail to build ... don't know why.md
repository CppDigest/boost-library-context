# #15 - Boost Algorithm docs fail to build ... don't know why [Open]

> Username: mclow  
> Created at: 2022-07-01 19:47:53 UTC  
> Updated at: 2022-09-24 12:41:20 UTC  
> Url: https://github.com/boostorg/quickbook/issues/15  

See https://github.com/boostorg/algorithm/commit/12ade334e0fd0f67f7aa90b755061f6f5863cd35#commitcomment-77319137  
  
Peter Dimov reports:  
> If I insert a newline before [endsect:copy_if_while] and [endsect:copy_if_until], it passes.

---

## Comment 1

> Username: Kojoley  
> Created at: 2022-09-23 23:37:50 UTC  
> Url: https://github.com/boostorg/quickbook/issues/15#issuecomment-1256798035  

Did you use GCC 9.2-12.1 to compile Quickbook? Could be a GCC bug https://github.com/boostorg/spirit/issues/737 which seems to be fixed in 12.2.

---

## Comment 2

> Username: mclow  
> Created at: 2022-09-24 00:11:29 UTC  
> Url: https://github.com/boostorg/quickbook/issues/15#issuecomment-1256809797  

I don't know about Peter, but I definitely did not, and I ran into this bug

---

## Comment 3

> Username: Lastique  
> Created at: 2022-09-24 12:41:19 UTC  
> Url: https://github.com/boostorg/quickbook/issues/15#issuecomment-1256960073  

Could you test if https://github.com/boostorg/quickbook/pull/18 helps your case?
