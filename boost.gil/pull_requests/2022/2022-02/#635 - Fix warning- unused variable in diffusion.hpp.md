# #635 Fix warning: unused variable in diffusion.hpp [Merged]

> Username: mloskot  
> Created at: 2022-02-05 12:59:34 UTC  
> Updated at: 2022-04-12 17:24:11 UTC  
> Merged at: 2022-02-06 18:11:26 UTC  
> Closed at: 2022-02-06 18:11:26 UTC  
> Url: https://github.com/boostorg/gil/pull/635  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
<!-- What does this pull request do? -->  
  
@simmplecoder  Could you confirm these were just unused and not meant to be used but overlooked?  
  
```  
../../boost/gil/image_processing/diffusion.hpp:160:14: warning: unused variable ‘first’ [-Wunused-variable]  
  160 |         auto first = stencil.begin();  
      |              ^~~~~  
../../boost/gil/image_processing/diffusion.hpp:161:14: warning: unused variable ‘last’ [-Wunused-variable]  
  161 |         auto last = stencil.end();  
      |              ^~~~  
```  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [ ] Review and approve

---

## Comment 1

> Username: simmplecoder  
> Created_at: 2022-02-06 11:19:50 UTC  
> Url: https://github.com/boostorg/gil/pull/635#issuecomment-1030811817  

It seems like I wanted to add a warning or error if the stencil is not of the correct size (it should be typical 9 pixel window), but backtracked on that because the function is usually called in a loop and rarely used on its own. If the error message is not needed, then it is safe to merge this. The reduction looks correct (it is 5 connectivity, diagonals and the center).

---
