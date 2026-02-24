# #1 - Add operators <<, >>, unary +, unary * [Closed]

> Username: pdimov  
> Created at: 2020-09-30 11:50:44 UTC  
> Updated at: 2021-09-04 23:55:31 UTC  
> Closed at: 2021-09-04 23:55:31 UTC  
> Url: https://github.com/boostorg/lambda2/issues/1  



---

## Comment 1

> Username: pdimov  
> Created at: 2021-03-27 05:50:44 UTC  
> Updated at: 2021-03-27 05:52:26 UTC  
> Url: https://github.com/boostorg/lambda2/issues/1#issuecomment-808663247  

Left shift is needed for "hello world" examples using `std::cout`: https://godbolt.org/z/PsKojK6va  
Unary plus is needed because `_1` by itself doesn't work, but `+_1` would: https://godbolt.org/z/1EeTdvYET

---

## Comment 2

> Username: pdimov  
> Created at: 2021-08-19 21:38:07 UTC  
> Url: https://github.com/boostorg/lambda2/issues/1#issuecomment-902265627  

This is mostly done, but I'm leaving it open because `std::cout << _1` doesn't work as-is, and maybe it needs to be special-cased to work. E.g. https://godbolt.org/z/ncPrGvKen

---

## Comment 3

> Username: pdimov  
> Created at: 2021-09-04 23:55:31 UTC  
> Url: https://github.com/boostorg/lambda2/issues/1#issuecomment-913056903  

Fixed in https://github.com/boostorg/lambda2/commit/e2251ab4502ea2e6fdcd69bcfc5cd6d3eedda88a, e.g. https://godbolt.org/z/qaMbWM7zh and https://godbolt.org/z/hnWTeaa94.
