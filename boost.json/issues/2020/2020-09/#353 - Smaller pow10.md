# #353 - Smaller pow10 [Closed]

> Username: vinniefalco  
> Created at: 2020-09-17 00:45:15 UTC  
> Updated at: 2021-04-26 06:32:25 UTC  
> Closed at: 2021-04-26 06:32:25 UTC  
> Url: https://github.com/boostorg/json/issues/353  

Using Peter's code we can use up less space in the implementation of pow10

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-02-25 05:51:18 UTC  
> Url: https://github.com/boostorg/json/issues/353#issuecomment-785632213  

Source?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-02-25 18:40:41 UTC  
> Url: https://github.com/boostorg/json/issues/353#issuecomment-786117451  

@pdimov ? Where was that godbolt example you wrote?

---

## Comment 3

> Username: pdimov  
> Created at: 2021-02-25 18:44:16 UTC  
> Url: https://github.com/boostorg/json/issues/353#issuecomment-786119664  

Somewhere in the archive of #boost-json I'd assume. I no longer recall what was in it.

---

## Comment 4

> Username: grisumbras  
> Created at: 2021-02-26 06:36:56 UTC  
> Url: https://github.com/boostorg/json/issues/353#issuecomment-786445549  

@vinniefalco do you remember what was in it?

---

## Comment 5

> Username: sdkrystian  
> Created at: 2021-02-26 13:02:55 UTC  
> Url: https://github.com/boostorg/json/issues/353#issuecomment-786634901  

Here is Peter's reference implementation: https://godbolt.org/z/K4cx6z  
  
Here is my version of it: https://godbolt.org/z/Pj71GG

---

## Comment 6

> Username: grisumbras  
> Created at: 2021-02-26 14:01:53 UTC  
> Url: https://github.com/boostorg/json/issues/353#issuecomment-786665203  

Thanks!

---

## Comment 7

> Username: grisumbras  
> Created at: 2021-04-26 06:21:30 UTC  
> Url: https://github.com/boostorg/json/issues/353#issuecomment-826540156  

I think, this should be closed. The approach we use currently results in higher precision.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2021-04-26 06:28:20 UTC  
> Url: https://github.com/boostorg/json/issues/353#issuecomment-826543732  

Close it
