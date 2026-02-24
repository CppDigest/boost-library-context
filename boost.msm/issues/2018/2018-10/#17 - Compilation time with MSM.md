# #17 - Compilation time with MSM [Closed]

> Username: UnsavedArtist  
> Created at: 2018-10-10 21:58:29 UTC  
> Updated at: 2024-02-08 15:32:20 UTC  
> Closed at: 2024-02-08 15:32:20 UTC  
> Url: https://github.com/boostorg/msm/issues/17  

Compiling a C++ file with over 20 states takes over 9GB of ram and takes over an hour to compile. It is creating a large object file with over half of the symbols coming from msm. Is there any solutions to this?

---

## Comment 1

> Username: henry-ch  
> Created at: 2024-02-08 15:32:20 UTC  
> Url: https://github.com/boostorg/msm/issues/17#issuecomment-1934378962  

A few:  
  
- https://www.boost.org/doc/libs/1_84_0/libs/msm/doc/HTML/ch03s05.html#d0e2542  
- upcoming backend11  
- redesigning the FSM. 20 States just has a feeling of monolith
