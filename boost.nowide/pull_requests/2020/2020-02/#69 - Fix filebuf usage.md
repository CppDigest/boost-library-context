# #69 Fix filebuf usage [Merged]

> Username: Flamefire  
> Created at: 2020-02-19 13:38:27 UTC  
> Updated at: 2020-02-21 13:05:13 UTC  
> Merged at: 2020-02-19 14:59:14 UTC  
> Closed at: 2020-02-19 14:59:14 UTC  
> Url: https://github.com/boostorg/nowide/pull/69  

After refactoring in https://github.com/boostorg/nowide/commit/ad77653f517e1bbac2f0076d7fc49b84e4630ed3 the old filebuf instance was used instead of the inherited one leading to wrong filebuf returned by `rdbuf`  
  
This was only found during implementing move ops as it simply initialized the underlying stream with a pointer to a non-constructed object which was (usually/always?) constructed before it was actually used and this worked so far. However this is actually not what was intended and may be UB if the stream ctor used the buffer

---
