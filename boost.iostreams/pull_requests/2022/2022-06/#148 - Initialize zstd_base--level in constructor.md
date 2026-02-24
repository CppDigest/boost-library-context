# #148 Initialize zstd_base::level in constructor [Open]

> Username: jwakely  
> Created at: 2022-06-23 10:24:58 UTC  
> Updated at: 2022-06-23 13:04:38 UTC  
> Url: https://github.com/boostorg/iostreams/pull/148  

It does actually get initialized when the derived class calls  
zstd_base::init but setting it in the constructor prevents static  
analyzers from complaining.  
  
A similar fix was made to `lzma_base` in 5692d3421962a0654f19d5c070431c7313fe4c92

---

## Comment 1

> Username: mclow  
> Created_at: 2022-06-23 13:04:38 UTC  
> Url: https://github.com/boostorg/iostreams/pull/148#issuecomment-1164383962  

LGTM

---
