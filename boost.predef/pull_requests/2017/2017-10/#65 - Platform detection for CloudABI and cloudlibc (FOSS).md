# #65 Platform detection for CloudABI and cloudlibc (FOSS) [Merged]

> Username: jeking3  
> Created at: 2017-10-19 01:39:31 UTC  
> Updated at: 2017-10-23 18:55:37 UTC  
> Merged at: 2017-10-23 18:54:02 UTC  
> Closed at: 2017-10-23 18:54:02 UTC  
> Url: https://github.com/boostorg/predef/pull/65  

Currently this is binary without any versioning information.  
Once I get more information about versioning I will add it later.  
See: https://github.com/NuxiNL/cloudabi/issues/3  
  
I set up the cross-compile environment and was able to get `info_as_c` to indicate the platform was `BOOST_PLAT_CLOUDABI`.  
  
Adds: `BOOST_PLAT_CLOUDABI` (not versioned) and `BOOST_LIB_C_CLOUDABI` with major and minor version based off https://github.com/NuxiNL/cloudlibc/commit/20e1f13abe2582d9126075a4a4e0b0971d105be8.

---

## Comment 1

> Username: jeking3  
> Created_at: 2017-10-21 22:45:04 UTC  
> Url: https://github.com/boostorg/predef/pull/65#issuecomment-338437488  

@grafikrobot build failures appear to be environmental.

---
