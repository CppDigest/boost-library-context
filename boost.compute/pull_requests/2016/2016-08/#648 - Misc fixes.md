# #648 Misc fixes [Merged]

> Username: jszuppe  
> Created at: 2016-08-22 18:35:45 UTC  
> Updated at: 2016-08-23 08:07:13 UTC  
> Merged at: 2016-08-23 04:20:02 UTC  
> Closed at: 2016-08-23 04:20:02 UTC  
> Url: https://github.com/boostorg/compute/pull/648  

This fixes:  
- Some typos   
- `C4267` warning when compiling with MSVC and OpenCL 2.0  
- `gather()` algorithm, `m_offset` was unnecessary and caused errors since offset is already stored in an iterator.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2016-08-23 04:19:56 UTC  
> Url: https://github.com/boostorg/compute/pull/648#issuecomment-241622682  

Looks good! Will try to get this into master before 1.62.

---
