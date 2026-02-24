# #196 Implement a pseudo-toolset for cross-compiling [Merged]

> Username: dm0-  
> Created at: 2017-05-05 00:12:07 UTC  
> Updated at: 2021-10-02 21:19:18 UTC  
> Merged at: 2019-02-23 15:36:24 UTC  
> Closed at: 2019-02-23 15:36:24 UTC  
> Url: https://github.com/boostorg/build/pull/196  

I didn't see a way to use the bootstrapping binaries while cross-compiling with the `cc` toolset.  Am I missing something?  If not, here is a patch that adds the option.  It supports building native bootstrapping binaries and cross-compiling everything else.

---
