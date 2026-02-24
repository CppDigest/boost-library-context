# #339 - Library-specific _NO_LIB macros should be recognized and disable autolink [Closed]

> Username: pdimov  
> Created at: 2022-06-04 11:11:41 UTC  
> Updated at: 2023-12-17 13:23:53 UTC  
> Closed at: 2023-12-17 13:23:53 UTC  
> Url: https://github.com/boostorg/test/issues/339  

The library-specific _DYN_LINK macros defined by the CMake config files are recognized (https://github.com/boostorg/test/commit/625bafd2cdc3e9a6f338573a17558050e2bd0e58) but the library-specific _NO_LIB macros are not.  
  
This used to not matter because the config files used to define BOOST_ALL_NO_LIB, but they define the library-specific _NO_LIB macros now.  
  
Ref: https://lists.boost.org/Archives/boost/2022/06/253153.php

---

## Comment 1

> Username: pdimov  
> Created at: 2023-12-16 15:10:45 UTC  
> Url: https://github.com/boostorg/test/issues/339#issuecomment-1858840214  

Ping? Autolink is still active which is at the moment visible in e.g. https://github.com/boostorg/boost_install/actions/runs/7232627306/job/19707018584#step:4:440 because the autolink version in Config is still not updated so it tries to autolink to 1.84.

---

## Comment 2

> Username: pdimov  
> Created at: 2023-12-16 15:23:45 UTC  
> Url: https://github.com/boostorg/test/issues/339#issuecomment-1858842585  

I believe that https://github.com/boostorg/test/pull/406 is the fix for this.
