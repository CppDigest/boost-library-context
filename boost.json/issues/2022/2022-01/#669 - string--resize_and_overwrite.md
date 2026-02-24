# #669 - string::resize_and_overwrite [Open]

> Username: vinniefalco  
> Created at: 2022-01-12 16:05:29 UTC  
> Updated at: 2022-01-15 15:59:34 UTC  
> Url: https://github.com/boostorg/json/issues/669  

We should add `string::resize_and_overwrite` as per P1072R10 (http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2021/p1072r10.html). The function `serialize_impl` can be changed to use it.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-01-12 16:05:40 UTC  
> Url: https://github.com/boostorg/json/issues/669#issuecomment-1011202710  

I guess we would want to deprecate `string::grow`

---

## Comment 2

> Username: grisumbras  
> Created at: 2022-01-15 14:56:59 UTC  
> Url: https://github.com/boostorg/json/issues/669#issuecomment-1013695693  

You mean, alternative implementation when C++23 is supported?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-01-15 15:40:24 UTC  
> Url: https://github.com/boostorg/json/issues/669#issuecomment-1013703250  

add it to `json::string`

---

## Comment 4

> Username: grisumbras  
> Created at: 2022-01-15 15:45:12 UTC  
> Url: https://github.com/boostorg/json/issues/669#issuecomment-1013704024  

But how would `serialize_impl` use it? It doesn't use `json::string`.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-01-15 15:46:48 UTC  
> Url: https://github.com/boostorg/json/issues/669#issuecomment-1013704294  

I thought there was an overload the serialized to `json::string`?

---

## Comment 6

> Username: grisumbras  
> Created at: 2022-01-15 15:58:56 UTC  
> Url: https://github.com/boostorg/json/issues/669#issuecomment-1013706216  

This doesn't seem to be the case.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-01-15 15:59:34 UTC  
> Url: https://github.com/boostorg/json/issues/669#issuecomment-1013706340  

We could add it. Anyway `json::string::resize_and_overwrite` is a better API than the current `json::string::grow`
