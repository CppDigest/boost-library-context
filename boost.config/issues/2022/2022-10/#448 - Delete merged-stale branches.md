# #448 - Delete merged/stale branches [Closed]

> Username: Flamefire  
> Created at: 2022-10-12 09:08:46 UTC  
> Updated at: 2022-11-20 17:46:19 UTC  
> Closed at: 2022-11-20 17:46:19 UTC  
> Url: https://github.com/boostorg/config/issues/448  

This repo has [49 branches](https://github.com/boostorg/config/branches/all), many of them from merged PRs. Can those be deleted please?  
  
It makes it quite hard to find a specific one and increases overhead for pulling/cloning this repo  
  
Simply check the 2nd number on each branch in the above link: If it is zero (i.e. not ahead of develop, which means it contains no commits not included in develop) the branch can be safely removed. The rest might need a quick check but I suppose most can be removed

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-11-20 17:46:19 UTC  
> Url: https://github.com/boostorg/config/issues/448#issuecomment-1321199284  

Done.
