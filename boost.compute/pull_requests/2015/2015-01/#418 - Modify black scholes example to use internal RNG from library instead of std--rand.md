# #418 Modify black scholes example to use internal RNG from library instead of std::rand [Open]

> Username: roshanrags  
> Created at: 2015-01-25 14:42:01 UTC  
> Updated at: 2016-10-19 04:04:48 UTC  
> Url: https://github.com/boostorg/compute/pull/418  

This adresses #276 . Also check if the values are correct... (esp. the put price)  
On my setup,  
  
```  
$ ./example/black_scholes  
device: GeForce GT 650M  
option 0 call price: 15.189  
option 0 put price: 0  
```  
  
UPDATE: Travis also reports the same value, so I guess the problem of different values on different platforms is solved. I have no clue about correctness though...

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-01-25 17:35:32 UTC  
> Url: https://github.com/boostorg/compute/pull/418#issuecomment-71382557  

The black-scholes code should still be correct, but different input values will give different output values. Having `0` exactly for the put price is a little weird, I'd try changing the RNG seed value to something else and then use those results.

---
