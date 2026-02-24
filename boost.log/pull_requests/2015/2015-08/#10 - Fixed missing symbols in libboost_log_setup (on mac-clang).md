# #10 Fixed missing symbols in libboost_log_setup (on mac/clang) [Merged]

> Username: crmoore  
> Created at: 2015-08-24 18:40:20 UTC  
> Updated at: 2015-08-25 15:17:15 UTC  
> Merged at: 2015-08-25 07:32:42 UTC  
> Closed at: 2015-08-25 07:32:42 UTC  
> Url: https://github.com/boostorg/log/pull/10  

Symbols are not getting properly exported from boost_log_setup when build on Clang on Mac.  
  
Switched the macro to use BOOST_SYMBOL_EXPORT, as is done for the boost_log library symbols.  
  
Tested on Mac OSX 10.10, XCode 6.4, Clang version:  
Apple LLVM version 6.1.0 (clang-602.0.53) (based on LLVM 3.6.0svn)  
Target: x86_64-apple-darwin14.4.0  
Thread model: posix

---

## Comment 1

> Username: Lastique  
> Created_at: 2015-08-25 07:32:49 UTC  
> Url: https://github.com/boostorg/log/pull/10#issuecomment-134510626  

Thanks.

---
