# #50 Fix infinite loop in gmp_float to fixed-point string conversion [Merged]

> Username: emfrias  
> Created at: 2018-03-15 21:08:33 UTC  
> Updated at: 2018-03-18 19:17:31 UTC  
> Merged at: 2018-03-18 19:17:19 UTC  
> Closed at: 2018-03-18 19:17:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/50  

You can trigger the infinite loop with:   
```cpp  
cout << setprecision(1) << fixed << mpf_float_50("0.0501");  
```

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2018-03-18 19:17:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/50#issuecomment-374036152  

Thanks for that!

---
