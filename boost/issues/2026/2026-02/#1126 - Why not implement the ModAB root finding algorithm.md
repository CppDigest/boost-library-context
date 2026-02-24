# #1126 - Why not implement the ModAB root finding algorithm: [Closed]

> Username: Proektsoftbg  
> Created at: 2026-02-14 02:21:59 UTC  
> Updated at: 2026-02-18 22:08:54 UTC  
> Closed at: 2026-02-18 22:08:54 UTC  
> Url: https://github.com/boostorg/boost/issues/1126  

https://iopscience.iop.org/article/10.1088/1757-899X/1276/1/012010  
  
Although it is relatively new and still not well known, the benchmarks show that it significantly outperform all other methods:  
https://github.com/Proektsoftbg/Numerical/blob/main/Numerical.Benchmark/doc/Root/Root-test-results.pdf  
  
```  
================================================================================  
  CATEGORY BREAKDOWN (average iterations per function)  
================================================================================  
Category                  bs    fp   mfp   ill    ab   ITP   mAB   rid    br   RBP  
----------------------------------------------------------------------------------  
Smooth well-behaved     48.0  22.0  13.0   9.2   7.5  12.8   8.1  11.0   8.8  10.8  
High-degree poly        48.0 147.1  22.6  42.4  79.1  34.2  18.3  25.2  35.7  44.4  
Steep/exponential       43.3  84.2  16.9  12.9  30.8  25.0   9.8  11.4   9.9  20.0  
Oscillatory             48.0  11.6  11.9   7.6   6.3  28.0   7.6  13.0   7.5   9.6  
Near-flat/slow          38.6 102.4  20.7  43.8  83.1  27.8  19.5  28.0  40.1  59.6  
Wide brackets           48.0  94.2  56.6  23.8  59.0  34.4  17.4  21.2  17.0  21.6  
Asymmetric brackets     48.0 130.0  19.4  24.8  55.2  31.8  15.6  21.6  17.0  31.6  
  
================================================================================  
  FINAL RANKING (by total iterations across 60 functions, lower = better)  
================================================================================  
   1. mAB    total=  798  avg= 13.3  fail=0  (baseline)  ######  
   2. rid    total= 1100  avg= 18.3  fail=0      (+38%)  #########  
   3. br     total= 1190  avg= 19.8  fail=0      (+49%)  #########  
   4. mfp    total= 1231  avg= 20.5  fail=1      (+54%)  ##########  
   5. ill    total= 1402  avg= 23.4  fail=1      (+76%)  ###########  
   6. ITP    total= 1609  avg= 26.8  fail=0     (+102%)  #############  
   7. RBP    total= 1710  avg= 28.5  fail=2     (+114%)  ##############  
   8. ab     total= 2639  avg= 44.0  fail=8     (+231%)  #####################  
   9. bs     total= 2739  avg= 45.6  fail=0     (+243%)  ######################  
  10. fp     total= 4794  avg= 79.9  fail=18    (+501%)  #######################################  
  
Legend:  
bs – Bisection method  
fp – False position   
mfp – Modified false position   
ill – Illinois method  
AB – Anderson-Bjork   
ITP – Interpolate, truncate, project   
mAB – Modified Anderson-Bjork (new)  
Rid – Ridders  
Brе – Brent  
```
