# #476 Rename strings.h to jam_strings.h to distinguish from POSIX strings.h. [Merged]

> Username: Lastique  
> Created at: 2019-09-02 18:14:54 UTC  
> Updated at: 2021-10-02 21:13:51 UTC  
> Merged at: 2019-09-11 03:25:13 UTC  
> Closed at: 2019-09-11 03:25:13 UTC  
> Url: https://github.com/boostorg/build/pull/476  

Some system headers on POSIX systems indirectly include `strings.h` in `extern "C"` region. This sometimes results in Boost.Build's `strings.h` being included into such region, which marks all `string_*` functions as `extern "C"` and changes their name mangling rules accordingly, which causes linking errors. To resolve this header conflict, this commit renames `strings.h` to `jam_strings.h`. And `strings.cpp` to `jam_strings.cpp` for consistency.  
  
Fixes https://github.com/boostorg/build/issues/468.

---
