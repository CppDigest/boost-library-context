# #166 - Log autolinks to DateTime [Closed]

> Username: pdimov  
> Created at: 2021-11-03 18:41:24 UTC  
> Updated at: 2021-11-04 01:48:06 UTC  
> Closed at: 2021-11-03 21:08:37 UTC  
> Url: https://github.com/boostorg/log/issues/166  

Here: https://github.com/boostorg/log/blob/d55f122f741b441e64b7f88bf1c3c4ed1d5d1feb/include/boost/log/detail/config.hpp#L254-L260  
  
Since https://github.com/boostorg/log/commit/a79103cb3dedb6582d24a09e553874e0a5b5f6bc removed the dependency in build/Jamfile, installing Log with `b2 --with-log install` does not build and install the DateTime stub library. Consequently, autolinking to it fails, as in https://github.com/boostorg/boost_install/runs/4087219181?check_suite_focus=true.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-11-03 21:08:58 UTC  
> Url: https://github.com/boostorg/log/issues/166#issuecomment-960072184  

Fixed, thanks.

---

## Comment 2

> Username: pdimov  
> Created at: 2021-11-03 23:46:11 UTC  
> Url: https://github.com/boostorg/log/issues/166#issuecomment-960299514  

Be sure to merge to master before today's deadline.

---

## Comment 3

> Username: Lastique  
> Created at: 2021-11-04 01:48:06 UTC  
> Url: https://github.com/boostorg/log/issues/166#issuecomment-960367432  

Merged.
