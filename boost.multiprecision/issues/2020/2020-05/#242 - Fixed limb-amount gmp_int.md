# #242 - Fixed limb-amount gmp_int [Open]

> Username: nemothenoone  
> Created at: 2020-05-27 12:20:59 UTC  
> Updated at: 2020-06-20 05:45:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/242  

Will fixed-limb amount ```gmp_int``` be welcomed? Why? To generalize the fixed-precision number concept (and for some of my own usage).  
  
Such an implementation will not be a straight ```mpz_t``` wrapper, but it will be built around GMP using the same concept, which is being used for fixed-precision ```cpp_int```.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-06-09 11:53:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/242#issuecomment-641240401  

In principle, though it would be tricky to get to work - would you have to call all the mpn_* functions directly?

---

## Comment 2

> Username: nemothenoone  
> Created at: 2020-06-20 05:45:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/242#issuecomment-646946182  

That is exactly. It will be handled with direct mpn_* function calls.  
  
I'm more concerned about formalizing _FixedPrecisionNumber_ concept, so such an extension could give us a reason to do so.
