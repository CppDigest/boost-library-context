# #49 Performance improvement [Merged]

> Username: BrianWeed  
> Created at: 2024-02-04 19:38:54 UTC  
> Updated at: 2024-02-06 06:47:03 UTC  
> Merged at: 2024-02-06 06:47:03 UTC  
> Closed at: 2024-02-06 06:47:03 UTC  
> Url: https://github.com/boostorg/function/pull/49  

Move functors where possible to reduce the number of copies (Lambdas with expensive-to-copy captures can be a bottleneck when copied)

---
