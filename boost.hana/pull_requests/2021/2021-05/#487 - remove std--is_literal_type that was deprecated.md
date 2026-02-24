# #487 remove std::is_literal_type that was deprecated [Merged]

> Username: sbolding-LANL  
> Created at: 2021-05-11 17:02:39 UTC  
> Updated at: 2021-05-26 14:21:27 UTC  
> Merged at: 2021-05-26 03:40:12 UTC  
> Closed at: 2021-05-26 03:40:12 UTC  
> Url: https://github.com/boostorg/hana/pull/487  

Supports #475  
  
- A deprecation warning is thrown (tested on gcc11 with  
std=c++17), so just remove the trait for > c++14.

---

## Comment 1

> Username: ricejasonf  
> Created_at: 2021-05-11 22:23:26 UTC  
> Url: https://github.com/boostorg/hana/pull/487#issuecomment-839233101  

It looks like the CI failed because dl.bintray.com support has sunsetted according to this: https://jfrog.com/blog/into-the-sunset-bintray-jcenter-gocenter-and-chartcenter/

---
