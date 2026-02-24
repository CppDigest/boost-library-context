# #813 - the peformance of radix sort  is lower than cub or hipPRIM [Open]

> Username: xqch1983  
> Created at: 2019-01-17 02:30:34 UTC  
> Updated at: 2019-01-17 09:08:33 UTC  
> Url: https://github.com/boostorg/compute/issues/813  

if we have do some work to improve the performance of radix_sort_by_key( ), as i tested , the perf is 11ms per 1m element size. while ~1.15ms in rocmPRIM(OpenCL) and CUB(cuda) per 1M elements

---

## Comment 1

> Username: jszuppe  
> Created at: 2019-01-17 07:31:41 UTC  
> Url: https://github.com/boostorg/compute/issues/813#issuecomment-455070259  

Yeah, the performance of this algorithm was not improved for some time. We should check how rocPRIM does it and try similar things.

---

## Comment 2

> Username: jszuppe  
> Created at: 2019-01-17 09:08:33 UTC  
> Url: https://github.com/boostorg/compute/issues/813#issuecomment-455096131  

btw. rocPRIM is not implemented in OpenCL. It's HIP and HC (AMD's C++AMP).
