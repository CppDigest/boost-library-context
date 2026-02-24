# #108 Move BOOST_NO_CXX14_CONSTEXPR to the right section [Merged]

> Username: Lastique  
> Created at: 2016-12-27 16:38:44 UTC  
> Updated at: 2016-12-27 19:08:38 UTC  
> Merged at: 2016-12-27 19:08:38 UTC  
> Closed at: 2016-12-27 19:08:38 UTC  
> Url: https://github.com/boostorg/config/pull/108  

- Define BOOST_NO_CXX14_CONSTEXPR only for the gcc-compatible version of Intel compiler (other versions untested). The previous definition was not in the correct preprocessor branch and the macro was not defined when it should have been.  
- Increased the latest tested Intel compiler version to 17.  
- Added comments to the preprocessor directives to simplify navigation in the file.

---
