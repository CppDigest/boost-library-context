# #424 - Bring CI back to GCC 5 [Closed]

> Username: ckormanyos  
> Created at: 2022-01-31 16:55:20 UTC  
> Updated at: 2022-02-02 21:09:12 UTC  
> Closed at: 2022-02-02 21:09:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/424  

Not sure how difficult this might be. But after getting stable in standalone, I'd like to see how difficult it would be to support GCC 5 in CI.  
  
Based on [such lines](https://github.com/boostorg/multiprecision/blob/d7a0a2baed1e1c12118e4154217f65fbccdf778f/.github/workflows/multiprecision.yml#L360) and previous discussions, we stopped at GCC 6. But supporting C++11, it kind of makes sense to include GCC 5 as well.  
  
See also https://github.com/boostorg/boost/issues/610

---

## Comment 1

> Username: ckormanyos  
> Created at: 2022-01-31 16:56:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/424#issuecomment-1025993922  

This might be something we could try for in 1.80 after stably reaching standalone.
