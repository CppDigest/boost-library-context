# #111 Add test showing the crash in the context of shared libraries [Closed]

> Username: Flamefire  
> Created at: 2018-07-12 08:44:11 UTC  
> Updated at: 2024-12-09 10:29:16 UTC  
> Closed at: 2018-11-17 19:28:46 UTC  
> Url: https://github.com/boostorg/serialization/pull/111  

This adds a test (from #105) which uses singletons in shared libraries linked against static boost. This crashes on termination as explained in #105, the linked issue and the mailing list.  
  
As static boost is used in shared libraries `-fPIC` is required.

---

## Comment 1

> Username: robertramey  
> Created_at: 2018-11-17 19:28:42 UTC  
> Url: https://github.com/boostorg/serialization/pull/111#issuecomment-439641546  

I believe that this is resolved.  If anyone is unsatisfied with this, please open a new PR

---
