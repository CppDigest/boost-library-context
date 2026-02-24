# #106 Mark exception classes as BOOST_SYMBOL_VISIBLE [Merged]

> Username: pdimov  
> Created at: 2021-02-02 20:29:39 UTC  
> Updated at: 2021-05-28 14:37:37 UTC  
> Merged at: 2021-05-28 14:37:37 UTC  
> Closed at: 2021-05-28 14:37:37 UTC  
> Url: https://github.com/boostorg/program_options/pull/106  

Under libc++, exception classes need to be marked as BOOST_SYMBOL_VISIBLE in both the library and the user code, otherwise they can't be caught across a .so boundary. While BOOST_SYMBOL_EXPORT already expands to BOOST_SYMBOL_VISIBLE, BOOST_SYMBOL_IMPORT does not, so _VISIBLE needs to be added even when _DECL is present.  
  
This fixes the test failures under macOS.

---
