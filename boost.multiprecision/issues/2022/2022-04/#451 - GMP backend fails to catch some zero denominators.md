# #451 - GMP backend fails to catch some zero denominators [Closed]

> Username: ckormanyos  
> Created at: 2022-04-26 05:14:03 UTC  
> Updated at: 2022-04-30 13:30:16 UTC  
> Closed at: 2022-04-30 13:30:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/451  

In the GMP backend in `gmp.hpp`, some uses of quotients such as in [here](https://github.com/boostorg/multiprecision/blob/5c773e67703d9f0a896209de47f00f9253c3a3ca/include/boost/multiprecision/gmp.hpp#L3004) have a _hidden_ quotient and the code does not explicitly check for divide by zero.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2022-04-26 05:19:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/451#issuecomment-1109355304  

See also the discussion [here](https://github.com/boostorg/multiprecision/pull/448#issuecomment-1108425173)

---

## Comment 2

> Username: ckormanyos  
> Created at: 2022-04-26 17:58:27 UTC  
> Updated at: 2022-04-26 17:58:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/451#issuecomment-1110091695  

This issue is being handled in #452

---

## Comment 3

> Username: ckormanyos  
> Created at: 2022-04-30 13:30:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/451#issuecomment-1113989491  

Fixed by #452 and with larger scope in `number` class design by #453
