# #37 Feature/ublas00004 simd gemm [Closed]

> Username: imre-palik  
> Created at: 2016-03-06 09:37:04 UTC  
> Updated at: 2016-05-23 21:01:00 UTC  
> Closed at: 2016-05-23 21:01:00 UTC  
> Url: https://github.com/boostorg/ublas/pull/37  

Hi all,  
  
this is the bulk of the gemm-based dense matrix multiplication implementation.  
- It contains the basic infrastructure,  
- Optimised vectorised kernels using gcc  
- Optimal kernel parameters for float, double, and long double for Intel Haswell uarch  
- Extension to the range of some benchmarks to show the case when it shines.  
  
what is missing:  
- optimised kernels for complex numbers  
  
caveats:  
- only tested with gcc on x86.  I can probably do clang, but I don have the infra for anything else.  
  
Cheers,  
  
Imre

---

## Comment 1

> Username: nasosi  
> Created_at: 2016-03-14 15:11:29 UTC  
> Url: https://github.com/boostorg/ublas/pull/37#issuecomment-196360794  

Please perform the pull request in https://github.com/uBLAS/ublas and not in boostorg.

---

## Comment 2

> Username: nasosi  
> Created_at: 2016-04-11 13:07:03 UTC  
> Url: https://github.com/boostorg/ublas/pull/37#issuecomment-208331310  

David can you please close this request ( do no merge)

---
