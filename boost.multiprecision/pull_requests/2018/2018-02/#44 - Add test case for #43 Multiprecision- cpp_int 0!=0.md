# #44 Add test case for #43 Multiprecision: cpp_int 0!=0 [Closed]

> Username: ohhmm  
> Created at: 2018-02-05 21:35:18 UTC  
> Updated at: 2018-02-08 10:42:43 UTC  
> Closed at: 2018-02-08 08:46:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/44  

implemented sign changing for increment of negative numbers

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2018-02-08 08:46:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/44#issuecomment-364041946  

Please see: https://github.com/boostorg/multiprecision/commit/0708fb99d3e1f339eabc2ccbbf63e1c895ddd31e

---

## Comment 2

> Username: ohhmm  
> Created_at: 2018-02-08 10:42:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/44#issuecomment-364072764  

Also take a look onto eval_increment fix: check for becoming zero to change sign.

---
