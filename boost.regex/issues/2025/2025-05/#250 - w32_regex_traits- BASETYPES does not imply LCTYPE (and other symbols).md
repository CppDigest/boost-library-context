# #250 - w32_regex_traits: BASETYPES does not imply LCTYPE (and other symbols) [Open]

> Username: boimart1  
> Created at: 2025-05-20 16:59:19 UTC  
> Updated at: 2025-05-21 21:33:24 UTC  
> Url: https://github.com/boostorg/regex/issues/250  

When specific Windows headers are selectively included before boost/regex is included, `w32_regex_traits.hpp` incorrectly assumes `LCTYPE` and various other symbols are defined.  
  
This appears to be due to the check for `BASETYPES`:  
  
https://github.com/boostorg/regex/blob/f851a08050dd6d4bdea552d2cf614c69d8d3cff9/include/boost/regex/v5/w32_regex_traits.hpp#L121  
  
This macro is defined in `minwindef.h`, But `LCTYPE` is actually defined in `winnls.h`. This check only holds if `minwindef.h` is transitively included through `windows.h`, which will itself include `winnls.h`.  
  
As such, the following minimum example code fails to compile with the following error:  
  
```c++  
#define _AMD64_  // assuming x64  
#include <windef.h>  
#include <boost/regex.hpp>  
```  
```  
C:\.conan\3072f8\1\include\boost-1_86\boost\regex\v5\w32_regex_traits.hpp(200): error C2061: syntax error: identifier 'LCTYPE'  
// various other errors for unknown identifiers  
```  
  
In my project, this problem appeared after updating [CLI11](https://github.com/CLIUtils/CLI11) from 2.1.2 to 2.4.2. In header-only mode, the new version includes only a few Windows headers in its implementation ([link](https://github.com/CLIUtils/CLI11/blob/v2.4.2/include/CLI/impl/Argv_inl.hpp#L48)).
