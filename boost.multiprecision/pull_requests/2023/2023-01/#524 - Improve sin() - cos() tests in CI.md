# #524 Improve sin() / cos() tests in CI [Merged]

> Username: ckormanyos  
> Created at: 2023-01-22 13:43:29 UTC  
> Updated at: 2023-01-22 16:13:09 UTC  
> Merged at: 2023-01-22 16:13:04 UTC  
> Closed at: 2023-01-22 16:13:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/524  

The purpose of this PR is to improve `sin()` / `cos()` tests. These had previously relied upon non-intuitive, non-portable overflow of the index-type `unsigned`.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2023-01-22 14:29:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/524#issuecomment-1399504493  

See also #523

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2023-01-22 14:36:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/524#issuecomment-1399506050  

I ended up generating test data as follows for [cos](https://www.wolframalpha.com/input?i=Table%5BN%5BCos%5B%28EulerGamma+%2850+%28k+-+50%29%29%29+%2B+%281%2F3%29%5D%2C+101%5D%2C+%7Bk%2C+0%2C+100%2C+1%7D%5D) and [sin](https://www.wolframalpha.com/input?i=Table%5BN%5BSin%5B%28EulerGamma+%2850+%28k+-+50%29%29%29+%2B+%281%2F3%29%5D%2C+101%5D%2C+%7Bk%2C+0%2C+100%2C+1%7D%5D)  
  
```  
Table[N[Cos[(EulerGamma (50 (k - 50))) + (1/3)], 101], {k, 0, 100, 1}]  
Table[N[Sin[(EulerGamma (50 (k - 50))) + (1/3)], 101], {k, 0, 100, 1}]  
```

---
