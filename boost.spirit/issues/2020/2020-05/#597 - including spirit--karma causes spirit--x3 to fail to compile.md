# #597 - including spirit::karma causes spirit::x3 to fail to compile [Closed]

> Username: rockonedege  
> Created at: 2020-05-10 13:07:45 UTC  
> Updated at: 2020-05-10 14:21:20 UTC  
> Closed at: 2020-05-10 13:42:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/597  

I am trying to use `x3` and `karma` in the same file. It compiles ok before adding the karma header  
```cpp  
#include <boost/spirit/include/karma.hpp>   
```  
once included, it generates the following errors. It looks to me likes name lookup issue, but I don't know how to fix it.  
  
I am on Windows 10, VS 2019.  
-- Selecting Windows SDK version 10.0.18362.0 to target Windows 10.0.19619.  
-- Environment Info  
   -- Compiler ID  - CMAKE_CXX_COMPILER_ID = MSVC  
   -- C++ Standard - CMAKE_CXX_STANDARD  = 17   
   -- Target = 64 bits build  
  
>F:\vcpkg\installed\x64-windows\include\boost/spirit/home/x3/binary/binary.hpp(88,1): error C2039: 'order': is not a member of 'boost::spi   
rit::endian' [F:\cc.vcxproj]  
F:\vcpkg\installed\x64-windows\include\boost/spirit/home/x3/binary/binary.hpp(88,29): error C2039: 'conditional_reverse': is not a member   
of 'boost::spirit::endian' [F:\cc.vcxproj]  
F:\vcpkg\installed\x64-windows\include\boost/spirit/home/x3/binary/binary.hpp(88,1): error C2065: 'conditional_reverse': undeclared ident   
ifier [F:\cc.vcxproj]  
F:\vcpkg\installed\x64-windows\include\boost/spirit/home/x3/binary/binary.hpp(88,65): error C3083: 'order': the symbol to the left of a '   
::' must be a type [F:\cc.vcxproj]  
F:\vcpkg\installed\x64-windows\include\boost/spirit/home/x3/binary/binary.hpp(88,72): error C2039: 'native': is not a member of 'boost::s   
pirit::endian' [F:\cc.vcxproj]  
F:\vcpkg\installed\x64-windows\include\boost/spirit/home/x3/binary/binary.hpp(88,1): error C2065: 'native': undeclared identifier [F:\cc.vcxproj]  
here is a complete minimal example:  
```cpp  
  
  
#include <boost/config/warning_disable.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
#include <boost/fusion/adapted/std_tuple.hpp>  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/binary.hpp>  
#include <boost/predef/other/endian.h>  
  
#include <boost/spirit/include/karma.hpp> // adding this file would fail to compile  
  
#include <iostream>  
  
#include <cstdint>  
#include <utility>  
#include <string>  
  
namespace header::ast  
{  
  
struct header_type  
{  
    std::uint16_t device_id;  
};  
} // namespace header::ast  
  
  
BOOST_FUSION_ADAPT_STRUCT(  
    header::ast::header_type,  
    device_id)  
  
namespace header::parser  
{  
namespace x3 = boost::spirit::x3;  
namespace ascii = boost::spirit::x3::ascii;  
  
x3::rule<class header, ast::header_type> const header = "header";  
  
auto const header_def =  
    x3::big_word;  
  
BOOST_SPIRIT_DEFINE(header);  
  
} // namespace header::parser  
  
  
namespace header::api  
{  
  
void parse(const std::string &s)  
{  
    namespace x3 = boost::spirit::x3;  
    namespace ast = header::ast;  
    namespace parser = header::parser;  
  
    ast::header_type h;  
  
    auto iter = s.cbegin();  
    auto const iter_end = s.cend();  
    bool r = x3::parse(iter, iter_end, parser::header, h);  
  
}  
  
// the following code is kept to show the purpose of #include <boost/spirit/include/karma.hpp>   
// no need to un-comment to reproduce the bug  
  
// std::string generate(const ast::header_type &h)  
// {  
  
//     namespace karma = boost::spirit::karma;  
  
//     std::string result;  
  
//     karma::generate(  
//         std::back_inserter(result), // the output  
//         karma::big_word,  
//         h // the input  
//     );  
  
// }  
} // namespace header::generator  
  
int main() {}  
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2020-05-10 13:42:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/597#issuecomment-626330140  

You should be using an outdated Boost, there is no more `boost::spi  
rit::endian` and the issue has been fixed several releases ago, in 1.70, in both Endian and Spirit. The example you have provided compiles fine using upstream Boost and VS2019 compiler.  
  
Closing as duplicate of #481.

---

## Comment 2

> Username: rockonedege  
> Created at: 2020-05-10 14:07:43 UTC  
> Updated at: 2020-05-10 14:08:41 UTC  
> Url: https://github.com/boostorg/spirit/issues/597#issuecomment-626333682  

No it's not.  At least  it was not fixed to a nicety. Your comment that boost::spirit::endian was gone was what the errors are about: it cannot be found.  
  
I am using boost 1.72 through vcpkg. Having read your message, I just `git pull` the latest from spirit repo and confirm that the source is the same.  
  
I somehow  fixed it myself  with the following change.  
  
Change `line# 88` in `\boost\spirit\home\x3\binary\binary.hpp`   
from   
```cpp  
endian::conditional_reverse<endian, endian::order::native>(attr_)  
```  
to   
```  
boost::endian::conditional_reverse<endian, boost::endian::order::native>(attr_)  
```  
  
I could submit a PR if this is the right fix.

---

## Comment 3

> Username: Kojoley  
> Created at: 2020-05-10 14:21:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/597#issuecomment-626335704  

I see, it is other way around - it was introduced in 1.70, but fixed in 1.73 by #550.
