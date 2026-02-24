# #685 - Duplicated #include in the doc [Closed]

> Username: mglisse  
> Created at: 2025-04-29 20:49:07 UTC  
> Updated at: 2025-06-28 20:11:30 UTC  
> Closed at: 2025-06-28 20:11:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/685  

https://github.com/boostorg/multiprecision/blob/0acb5b4aac63ef266fbe94549bfbc5f463695f6e/example/gmp_snips.cpp#L78-L79  
I don't know if one should simply disappear, or if a copy-paste-replace was interrupted before the replace stage and some other include was intended.  
  
(in case I got the wrong file in github, I am talking about the example at the bottom of https://www.boost.org/doc/libs/1_88_0/libs/multiprecision/doc/html/boost_multiprecision/tut/rational/gmp_rational.html)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2025-04-30 08:17:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/685#issuecomment-2841186291  

Accidental duplication: we should just remove one.  Thanks for spotting this!

---

## Comment 2

> Username: ckormanyos  
> Created at: 2025-06-28 20:11:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/685#issuecomment-3016010622  

Fixed by #704
