# #352 - Extend arithmetic operator support for accumulators [Open]

> Username: HDembinski  
> Created at: 2022-02-08 16:20:16 UTC  
> Updated at: 2022-02-08 16:24:11 UTC  
> Url: https://github.com/boostorg/histogram/issues/352  

All accumulators support addition and scaling (multiplication by a constant). Additionally, we could support subtraction, multiplication, and division.  
  
Subtraction  
- [ ] count  
- [ ] sum  
- [ ] weighted_sum  
- [ ] mean  
- [ ] weighted_mean  
  
Multiplication  
- [ ] count  
- [ ] sum  
- [ ] weighted_sum  
- [ ] mean  
- [ ] weighted_mean  
  
Division  
- [ ] count  
- [ ] sum  
- [x] weighted_sum  
- [ ] mean  
- [ ] weighted_mean  
  
How to implement division and multiplication for weighted mean is mathematically not clear to me.
