# #246 - When looking up environment variables, boost process assumes the keys are of equal size [Open]

> Username: kjetilj  
> Created at: 2022-04-28 05:50:27 UTC  
> Updated at: 2022-06-05 22:14:34 UTC  
> Url: https://github.com/boostorg/process/issues/246  

This causes a heap-buffer-overflow detected by ASAN  
https://godbolt.org/z/Eno9r737M  
  
https://github.com/boostorg/process/blob/develop/include/boost/process/environment.hpp#L263-L269

---

## Comment 1

> Username: oliness  
> Created at: 2022-06-05 22:14:34 UTC  
> Url: https://github.com/boostorg/process/issues/246#issuecomment-1146892552  

Issue fixed in latest commit to environment.hpp
