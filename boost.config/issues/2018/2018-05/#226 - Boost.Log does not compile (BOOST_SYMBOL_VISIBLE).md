# #226 - [clang][Windows] Boost.Log does not compile (BOOST_SYMBOL_VISIBLE) [Closed]

> Username: hia3  
> Created at: 2018-05-08 03:48:55 UTC  
> Updated at: 2018-08-03 17:21:30 UTC  
> Closed at: 2018-08-03 17:21:30 UTC  
> Url: https://github.com/boostorg/config/issues/226  

The commit https://github.com/boostorg/config/commit/3b709bc3edd331c114b0cce7f030441e032fa146 breaks Boost.Log when compiling with clang on Windows.  
  
To reproduce:  
  
    "C:\Program Files\LLVM\bin\clang++.exe" -fsyntax-only boost\boost\log\attributes\attribute.hpp -Iboost -std=c++2a  
  
Error:  
  
[boost\boost\log\attributes\attribute.hpp:69:5](https://github.com/boostorg/log/blob/develop/include/boost/log/attributes/attribute.hpp#L69): error: declaration of anonymous struct must be a definition  
    struct BOOST_LOG_NO_VTABLE BOOST_SYMBOL_VISIBLE impl :  
  
This happens because (after aforementioned commit) BOOST_SYMBOL_VISIBLE expands to `__attribute__((__visibility__("default")))` which is not recognized by clang on Windows.
