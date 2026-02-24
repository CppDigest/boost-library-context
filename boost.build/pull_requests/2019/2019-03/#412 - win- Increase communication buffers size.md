# #412 win: Increase communication buffers size [Merged]

> Username: Kojoley  
> Created at: 2019-03-17 15:30:15 UTC  
> Updated at: 2021-10-02 21:19:11 UTC  
> Merged at: 2019-03-17 19:10:41 UTC  
> Closed at: 2019-03-17 19:10:41 UTC  
> Url: https://github.com/boostorg/build/pull/412  

Currently the size of reading buffer is 16KiB while the the pipe buffer is of system default size which seems to be 8KiB on Win7. Because of this the half of the reading buffer is never used.  
  
Also, recent Windows updates with Meltdown mitigation made syscalls more expensive, and increasing the buffer size will lower the syscalls count.  
  
The change is 5x speed-up for me on `b2 -j2 toolset=msvc-10.0 cxxflags=/W4 qi_repo_keywords` (498 -> 106 seconds).

---
