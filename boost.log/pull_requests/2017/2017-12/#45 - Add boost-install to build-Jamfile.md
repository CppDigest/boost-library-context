# #45 Add boost-install to build/Jamfile [Merged]

> Username: pdimov  
> Created at: 2017-12-17 17:16:56 UTC  
> Updated at: 2017-12-17 17:35:39 UTC  
> Merged at: 2017-12-17 17:35:17 UTC  
> Closed at: 2017-12-17 17:35:17 UTC  
> Url: https://github.com/boostorg/log/pull/45  

This adds `stage` and `install` targets to the build Jamfile. In addition to being able to issue f.ex. `b2 stage` or `b2 install` from the `build` dir, this also allows enumeration of the library targets from an external Jamfile such as the one in https://github.com/boostorg/check_build/tree/develop/test. Without a stage target to query, the libraries declared with `lib something ;`, being main targets in the build Jamfile, are also picked up, and this is undesirable.  
  
An alternative might be to declare those explicit, but using `boost-install` is good practice anyway.

---
