# #170 - Jacobi symbol [Open]

> Username: nemothenoone  
> Created at: 2019-11-21 11:54:06 UTC  
> Updated at: 2020-07-19 13:39:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/170  

Is Jacobi symbol computation related to Boost.Multiprecision or to Boost.Math?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-11-24 12:03:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/170#issuecomment-557882166  

Good question: arguably it belongs in Boost.Integer ;)  
  
But I think adding it to Multiprecision is fine if this is part of the modular arithmetic code?

---

## Comment 2

> Username: nemothenoone  
> Created at: 2020-05-27 12:30:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/170#issuecomment-634627459  

Well, seems like, it goes to Boost.Integer.

---

## Comment 3

> Username: nemothenoone  
> Created at: 2020-07-19 13:23:21 UTC  
> Updated at: 2020-07-19 13:39:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/170#issuecomment-660643188  

Okay. Not closed yet. Jacobi symbol computation requires ```lsb``` function usage which is located inside the Boost.Multiprecision. Or some equivalent of ```low_zero_bits```. Last option actually requires counting limbs, so this, once again, makes the Jacobi symbol computation function belong to Boost.Multiprecision.  
  
I doubt we can introduce Boost.Multiprecision dependency for Boost.Integer. What do you think, @jzmaddock?
