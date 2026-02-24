# #38 Portable binary archive [Open]

> Username: pfligersdorffer  
> Created at: 2016-02-20 22:52:27 UTC  
> Updated at: 2016-02-24 21:38:17 UTC  
> Url: https://github.com/boostorg/serialization/pull/38  

I pushed some of the changes that I had done prior to renaming into little-archive to a separate portable-binary-archive branch. That branch is ready to be pulled back into the upstream portable_binary_archive branch.  
  
Portable archives now run all unit tests successfully with the exception of serializing a user defined primitive type. This is no surprise as it must fail at this time. Polymorphic portable archives face some more difficulties that need to be looked into. In addition the code has become simpler and classes reside in boost namespace.

---
