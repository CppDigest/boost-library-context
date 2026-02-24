# #114 - uBLAS FAQ [Closed]

> Username: JAMMIASHOK  
> Created at: 2021-05-01 08:16:01 UTC  
> Updated at: 2021-05-01 23:06:26 UTC  
> Closed at: 2021-05-01 23:06:26 UTC  
> Url: https://github.com/boostorg/ublas/issues/114  

I see the following question in the boost ublas link https://www.boost.org/doc/libs/1_76_0/libs/numeric/ublas/doc/index.html.  
Down the page FAQ - 1  
"Should I use uBLAS for new projects?  
A: At the time of writing (09/2012) there are a lot of good matrix libraries available, e.g., MTL4, armadillo, eigen. uBLAS offers a stable, well tested set of vector and matrix classes, the typical operations for linear algebra and solvers for triangular systems of equations. uBLAS offers dense, structured and sparse matrices - all using similar interfaces. And finally uBLAS offers good (but not outstanding) performance. On the other side, the last major improvement of uBLAS was in 2008 and no significant change was committed since 2009. So one should ask himself some questions to aid the decision: Availability? uBLAS is part of boost and thus available in many environments. Easy to use? uBLAS is easy to use for simple things, but needs decent C++ knowledge when you leave the path. Performance? There are faster alternatives. Cutting edge? uBLAS is more than 10 years old and missed all new stuff from C++11."  
Is there any effort being made to develop it further ?

---

## Comment 1

> Username: bassoy  
> Created at: 2021-05-01 11:30:56 UTC  
> Url: https://github.com/boostorg/ublas/issues/114#issuecomment-830617654  

@JAMMIASHOK    
Thanks for your question(s)!  
  
> Is there any effort being made to develop it further ?  
  
Yes. We are currently implementing and experimenting with different tensor types. Once the types are SOLID enough we intend to create new matrix and vector types by specializing the tensor types. Note that the one new tensor type is already available since 2019 - Boost Version 1.73. You can follow the status of some project [definitions](https://github.com/boostorg/ublas/projects).  
  
> On the other side, the last major improvement of uBLAS was in 2008 and no significant change was committed since 2009.  
  
The Boost.uBlas team currently investigates several design techniques for the new tensor, matrix and vector types. This includes the creation of C++20 concepts and new iterators/ranges for tensors, matrices and vectors. The goal is to provide a generic and a user-friendly API similar to the Matlab/Octave API with some special tweaks for tensor/matrix contraction such as the [Einstein notation](https://github.com/boostorg/ublas/wiki/Tensor#einstein-notation).  
  
> Availability? uBLAS is part of boost and thus available in many environments.  
  
Boost.uBlas will be available in future with better performance and an easier to use interface.  
  
> Performance? There are faster alternatives.   
  
We are also preparing fast functions with "near" outstanding performance for basic linear and multilinear algebra routines using the OpenMP 4.5 or 5.0 standard or even OpenCL for supporting GPUs.  
  
> Cutting edge? uBLAS is more than 10 years old and missed all new stuff from C++11  
> Easy to use? uBLAS is easy to use for simple things, but needs decent C++ knowledge when you leave the path.  
  
Boost.uBlas is reinventing itself by introducing generic multidimensional array types and a new convenient API to manipulate multidimensional arrays, see e.g. tensor [accessing](https://github.com/boostorg/ublas/wiki/Tensor#accessing-elements) or  [printing](https://github.com/boostorg/ublas/wiki/Tensor#formatted-output). It will be able to use and [interact](https://github.com/boostorg/ublas/wiki/Tensor#using-the-standard-library-algorithms) with C++20  standard library algorithms.   
  
  
If you have any further questions or suggestions please do not hesitate to post them here.   
Thanks!

---

## Comment 2

> Username: JAMMIASHOK  
> Created at: 2021-05-01 11:53:30 UTC  
> Url: https://github.com/boostorg/ublas/issues/114#issuecomment-830620301  

Thanks for the information. I look forward to use it !
