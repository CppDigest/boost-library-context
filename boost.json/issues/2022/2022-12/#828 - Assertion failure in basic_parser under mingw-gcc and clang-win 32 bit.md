# #828 - Assertion failure in basic_parser under mingw-gcc and clang-win 32 bit [Closed]

> Username: pdimov  
> Created at: 2022-12-28 15:35:40 UTC  
> Updated at: 2023-01-02 11:04:42 UTC  
> Closed at: 2023-01-02 11:04:42 UTC  
> Url: https://github.com/boostorg/json/issues/828  

I'm getting [CI failures](https://github.com/pdimov/mustache/actions/runs/3791897844/jobs/6447659398) with the following in the log:  
```  
====== BEGIN OUTPUT ======  
Assertion failed!  
  
Program: D:\a\mustache\boost-root\bin.v2\libs\mustache\test\spec_interpolation.test\gcc-8\debug\cxxstd-2a-iso\threading-multi\visibility-hidden\spec_interpolation.exe  
File: ./boost/json/basic_parser_impl.hpp, Line 1014  
  
Expression: *cs == '\x22'  
   
EXIT STATUS: 3   
====== END OUTPUT ======  
```  
The minimal program reproducing the failure under clang-win 32 bit is:  
```  
#include <boost/json.hpp>  
#include <boost/core/verbose_terminate_handler.hpp>  
#include <fstream>  
  
int main()  
{  
    std::set_terminate( boost::core::verbose_terminate_handler );  
    std::ifstream is( "interpolation.json" );  
    boost::json::parse( is );  
}  
```  
where `interpolation.json` is taken from here: https://github.com/mustache/spec/blob/5d3b58ea35ae309c40d7a8111bfedc4c5bcd43a6/specs/interpolation.json

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-12-28 16:05:12 UTC  
> Url: https://github.com/boostorg/json/issues/828#issuecomment-1366758918  

```  
Program: D:\a\mustache\boost-root\bin.v2\libs\mustache\test\spec_interpolation.test\gcc-8\debug\cxxstd-2a-iso\threading-multi\visibility-hidden\spec_interpolation.exe  
```  
Doesn't look like clang-win, looks more like mingw

---

## Comment 2

> Username: pdimov  
> Created at: 2022-12-28 16:06:46 UTC  
> Url: https://github.com/boostorg/json/issues/828#issuecomment-1366759976  

clang-win asserts in the same place, but that's not visible in the CI log because it times out due to the assertion popup.

---

## Comment 3

> Username: grisumbras  
> Created at: 2022-12-30 12:28:25 UTC  
> Url: https://github.com/boostorg/json/issues/828#issuecomment-1367894182  

My investigation discovered that this is the result of buffer, from which the parser reads, being put immediately before the parser object, and thus past-the-end address of the buffer becoming `this` address of the parser. So, #814 should fix the issue.

---

## Comment 4

> Username: grisumbras  
> Created at: 2022-12-30 19:46:34 UTC  
> Url: https://github.com/boostorg/json/issues/828#issuecomment-1368069093  

Should be fixed on develop. I checked locally, and the file in the description parses without errors.
