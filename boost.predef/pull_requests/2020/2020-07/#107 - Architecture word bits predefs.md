# #107 Architecture word bits predefs [Closed]

> Username: nemothenoone  
> Created at: 2020-07-09 13:00:37 UTC  
> Updated at: 2021-01-05 15:40:26 UTC  
> Closed at: 2021-01-05 15:40:26 UTC  
> Url: https://github.com/boostorg/predef/pull/107  

Each architecture has pre-defined word size in bits. Since this has no way to be detected correctly from compiler-only definitions (and `sizeof(int)` is obviously not the way as well), pre-defining according to the each architecture's documentation is the only way. So, here it is.  
  
Resolves https://github.com/NilFoundation/predef/issues/2.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2021-01-03 03:54:27 UTC  
> Url: https://github.com/boostorg/predef/pull/107#issuecomment-753563995  

Than you for the contribution.. I'm going to rework it locally into an equivalent as this is missing documentation and it would be closer to the library design to have a group of `BOOST_ARCH_WORD_SIZE_<arch-name>` definitions to follow current conventions. I'll also probably rework #108 to follow it.

---
