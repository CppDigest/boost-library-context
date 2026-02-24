# #511 - CI Failure with quadrature [Closed]

> Username: mborland  
> Created at: 2021-02-02 16:45:32 UTC  
> Updated at: 2021-06-13 19:45:52 UTC  
> Closed at: 2021-06-13 19:45:52 UTC  
> Url: https://github.com/boostorg/math/issues/511  

Several different types quadrature failures using GCC-9, CXXSTD=17, and ARCH=PPC64LE:  
  
[tanh_sinh_quadrature](https://travis-ci.org/github/mborland/math/jobs/756971706#L956)  
[exp_sinh_quadrature](https://travis-ci.org/github/mborland/math/jobs/756971706#L1121)  
[test_autodiff](https://travis-ci.org/github/mborland/math/jobs/756971706#L1430)  
  
Most of these seem to be related to __float128

---

## Comment 1

> Username: NAThompson  
> Created at: 2021-06-13 18:36:28 UTC  
> Url: https://github.com/boostorg/math/issues/511#issuecomment-860252770  

Can this be closed? It seems like we've gotten past this . . .
