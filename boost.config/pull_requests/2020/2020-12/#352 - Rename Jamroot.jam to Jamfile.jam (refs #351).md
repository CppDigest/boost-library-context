# #352 Rename Jamroot.jam to Jamfile.jam (refs #351) [Merged]

> Username: pdimov  
> Created at: 2020-12-29 17:43:06 UTC  
> Updated at: 2022-11-20 17:44:16 UTC  
> Merged at: 2020-12-30 18:58:22 UTC  
> Closed at: 2020-12-30 18:58:22 UTC  
> Url: https://github.com/boostorg/config/pull/352  

As explained in https://github.com/boostorg/config/issues/351, having a Jamroot causes all the build artifacts from the address model and architecture detection checks to end up in `libs/config/checks/architecture/bin`, instead of the build directory.

---
