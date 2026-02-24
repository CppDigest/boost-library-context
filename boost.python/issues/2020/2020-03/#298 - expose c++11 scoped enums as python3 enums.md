# #298 - expose c++11 scoped enums as python3 enums [Open]

> Username: finjulhich  
> Created at: 2020-03-27 06:47:19 UTC  
> Updated at: 2020-03-27 13:18:36 UTC  
> Url: https://github.com/boostorg/python/issues/298  

I don't believe this is possible atm?

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2020-03-27 11:53:30 UTC  
> Url: https://github.com/boostorg/python/issues/298#issuecomment-604959331  

Did you try ?  
They are handled just as ordinary (pre-C++11) enums.

---

## Comment 2

> Username: finjulhich  
> Created at: 2020-03-27 13:03:25 UTC  
> Url: https://github.com/boostorg/python/issues/298#issuecomment-604988614  

I actually meant the newer python34 enums

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2020-03-27 13:18:36 UTC  
> Url: https://github.com/boostorg/python/issues/298#issuecomment-604995753  

Oh, I see. Yes, that would be a backward-incompatible change, and thus requires a separate set of converters, or a compile-time flag to indicate which wrapper types to use.  
If you are interested in such a feature, I'd welcome patches / collaboration.
