# #712 - documentation makes stale reference to GMP 5.1 [Closed]

> Username: LegalizeAdulthood  
> Created at: 2025-07-08 04:56:11 UTC  
> Updated at: 2025-07-11 15:40:39 UTC  
> Closed at: 2025-07-11 15:40:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/712  

> note also that the [mpf_class](http://gmplib.org/manual/C_002b_002b-Interface-Floats.html#C_002b_002b-Interface-Floats) wrapper that will be supplied with **GMP-5.1** reduces the number of temporaries to pretty much zero  
  
https://www.boost.org/doc/libs/latest/libs/multiprecision/doc/html/boost_multiprecision/intro.html  
  
GMP is currently at 6.3.0, so I think talking about 5.1 in the future sense is stale.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2025-07-08 17:42:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/712#issuecomment-3049792846  

Good catch Richard. I'm not entirely sure if we can make 1.89, but then the next one for sure.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2025-07-10 19:36:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/712#issuecomment-3058745774  

See also #719
