# #1127 - Implement modAB algorithm for efficient root-finding [Open]

> Username: Proektsoftbg  
> Created at: 2026-02-18 22:05:48 UTC  
> Updated at: 2026-02-18 22:05:48 UTC  
> Url: https://github.com/boostorg/boost/issues/1127  

### Feature description  
  
This is a new and extremely efficient bracketing root-finding algorithm. It combines initial bisection steps with Anderson-Bjork's (AB) steps to achieve superlinear convergence (e.i. = 1.7-1.8), preserving optimality in worst case poorly behaved functions. ModAB uses clever method switching criteria, based on actually measuring the function linearity. According to the benchmarks, it **significantly outperforms** "classical" algorithms like ITP, Brent and Ridders:  
  
|Method	|bs   |fp   |mfp  |ill	|AB	  |ITP	|mAB  |Rid  |Bre  |  
|------:|----:|----:|----:|----:|----:|----:|----:|----:|----:|  
|SUM    |4176 |7709	|2089 |2684 |2700 |2581 |1227 |1990 |2625 |  
|AVERAGE    |46	  |85   |23   |29   |30   |28   |13   |22   |29   |  
|MAX    |48	  |200  |71   |200  |200  |50   |48   |78   |133  |  
  
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
  
More detailed results are provided here:  
https://github.com/Proektsoftbg/Numerical/blob/main/Numerical.Benchmark/doc/Root/Root-tests.pdf  
  
### Additional context  
  
The algorithm is presented in this paper:  
https://iopscience.iop.org/article/10.1088/1757-899X/1276/1/012010  
  
Here is the C# source code:  
https://github.com/Proektsoftbg/Numerical/blob/main/Numerical/Solver/ModAB.cs  
  
The algorithm is currently implemented in [Calcpad](https://github.com/Proektsoftbg/Calcpad), Jacob Willam's NASA/JSC [root-fortran](https://github.com/jacobwilliams/roots-fortran) and [Julia/NonlinearSolve.jl](https://github.com/SciML/NonlinearSolve.jl).
