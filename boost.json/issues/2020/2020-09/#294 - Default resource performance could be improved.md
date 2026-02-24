# #294 - Default resource performance could be improved [Closed]

> Username: sdkrystian  
> Created at: 2020-09-06 21:21:41 UTC  
> Updated at: 2020-09-10 17:42:01 UTC  
> Closed at: 2020-09-10 17:42:00 UTC  
> Url: https://github.com/boostorg/json/issues/294  

This is probably because we use a singleton.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-06 21:22:32 UTC  
> Url: https://github.com/boostorg/json/issues/294#issuecomment-687905210  

No it is because calls to the system `allocate` and `deallocate` are expensive

---

## Comment 2

> Username: sdkrystian  
> Created at: 2020-09-06 21:27:44 UTC  
> Url: https://github.com/boostorg/json/issues/294#issuecomment-687906331  

That's irrelevant, RapidJSON outperforms us anyways and they use `malloc` (which has identical performance)

---

## Comment 3

> Username: sdkrystian  
> Created at: 2020-09-10 17:42:00 UTC  
> Url: https://github.com/boostorg/json/issues/294#issuecomment-690559717  

Addressed by da65b25f4df7d3e090e4a700cdf14df026062c3c
