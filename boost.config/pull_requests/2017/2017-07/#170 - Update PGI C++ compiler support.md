# #170 Update PGI C++ compiler support [Merged]

> Username: dkolsen-pgi  
> Created at: 2017-07-27 22:26:39 UTC  
> Updated at: 2017-08-01 17:12:07 UTC  
> Merged at: 2017-08-01 17:12:07 UTC  
> Closed at: 2017-08-01 17:12:07 UTC  
> Url: https://github.com/boostorg/config/pull/170  

When the PGI C++ compiler changed to be GNU compatible, pgi.hpp stopped being used, because the check for __GNUC__ in select_compiler_config.hpp was true before the preprocessor ever got to the check for __PGI.  Rearrange the order of the checks in select_compiler_config.hpp, moving the check for __PGI above the check for __GNUC__.  
  
pgi.hpp was designed for a very old version of PGI C++, before it was GNU compatible.  The settings in that file won't work for PGI compilers of the last few years.  Replace the entire file with one that just includes gcc.hpp and then adjusts a few macros for areas where PGI is not quite GNU compatible.  (The old PGI compilers are not actively being used by any customers that we (PGI) know of, so keeping the old contents of pgi.hpp would be of little or no benefit.)

---
