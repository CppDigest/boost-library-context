# #1309 - Should d128 mul perform its own rounding? [Closed]

> Username: mborland  
> Created at: 2026-01-21 19:55:31 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2026-01-22 17:54:03 UTC  
> Url: https://github.com/boostorg/decimal/issues/1309  

Right now we divide the u256 so as to contain maximally what fits in u128, but we also know the exact digit count in the u128. Once we send the result to the d128 constructor we immediately have to count the digits of the u128, even though we already know this answer.

---

## Comment 1

> Username: mborland  
> Created at: 2026-01-21 20:31:58 UTC  
> Url: https://github.com/boostorg/decimal/issues/1309#issuecomment-3781036836  

The answer is yes, it's about 20% faster while also being more correct.
