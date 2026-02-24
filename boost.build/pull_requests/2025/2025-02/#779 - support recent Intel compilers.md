# #779 support recent Intel compilers [Closed]

> Username: MarDiehl  
> Created at: 2025-02-22 11:24:16 UTC  
> Updated at: 2025-02-22 11:24:31 UTC  
> Closed at: 2025-02-22 11:24:30 UTC  
> Url: https://github.com/boostorg/build/pull/779  

breaks for older, non-LLVM compilers. But oneAPI is around for quite some time and better to break old versions that the current versions.  
  
This solves  
- "icpx: error: unsupported argument 'h-create' to option '-pc'"  
- command not found: ifort  
  
I don't think that Fortran is used at all  
  
Thank you for your contributions. Main development of B2 has moved to  
  https://github.com/bfgroup/b2

---

## Comment 1

> Username: github-actions[bot]  
> Created_at: 2025-02-22 11:24:29 UTC  
> Url: https://github.com/boostorg/build/pull/779#issuecomment-2676155179  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
