# #509 - type conversion between std::complex and value [Open]

> Username: vinniefalco  
> Created at: 2021-02-28 05:47:56 UTC  
> Updated at: 2021-05-23 12:58:44 UTC  
> Url: https://github.com/boostorg/json/issues/509  

The library should support `std::complex` natively

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-02-28 05:48:57 UTC  
> Url: https://github.com/boostorg/json/issues/509#issuecomment-787400642  

This can come after array/tuple/pair (those are more important)

---

## Comment 2

> Username: grisumbras  
> Created at: 2021-05-23 07:09:08 UTC  
> Url: https://github.com/boostorg/json/issues/509#issuecomment-846515832  

Should we deduce `complex` by checking whether `x.real` and `x.imag` exist?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-05-23 12:58:44 UTC  
> Url: https://github.com/boostorg/json/issues/509#issuecomment-846559441  

that would be nice
