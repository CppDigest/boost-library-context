# #1287 - More decimal128_t add sticky bit [Closed]

> Username: mborland  
> Created at: 2025-12-17 15:22:44 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2026-01-14 00:13:55 UTC  
> Url: https://github.com/boostorg/decimal/issues/1287  

Since we don't promote to u256 for addition we lose information on the sticky bit of the smaller element. We should be able to make a synthetic routine like umul256 except we return the bool of the sign (if needed)
