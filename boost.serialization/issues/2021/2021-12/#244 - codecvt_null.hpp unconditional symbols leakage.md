# #244 - codecvt_null.hpp unconditional symbols leakage [Open]

> Username: claimred  
> Created at: 2021-12-30 08:31:03 UTC  
> Updated at: 2022-01-03 00:22:55 UTC  
> Url: https://github.com/boostorg/serialization/issues/244  

Why does `codecvt_null.hpp` file declares symbols with `BOOST_SYMBOL_EXPORT` instead of `BOOST_ARCHIVE_DECL`?  
  
https://github.com/boostorg/serialization/blob/develop/include/boost/archive/codecvt_null.hpp#L69  
  
https://github.com/boostorg/serialization/blob/develop/include/boost/archive/detail/decl.hpp#L26  
  
It leads to symbols leakage in DLLs statically linking boost.archive.

---

## Comment 1

> Username: robertramey  
> Created at: 2022-01-03 00:22:55 UTC  
> Url: https://github.com/boostorg/serialization/issues/244#issuecomment-1003802826  

It's probably a mistake as I had lots of difficulty with symbol export macros. Have you made this change and run the tests to verify that making this change doesn't break anything else.
