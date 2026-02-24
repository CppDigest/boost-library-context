# #247 - boost::u32regex does not compile on latest libc++ (19.0) due to use of UChar32 / int32_t as charT [Open]

> Username: JoachimSchurig  
> Created at: 2025-04-04 12:18:24 UTC  
> Updated at: 2025-04-05 00:18:45 UTC  
> Url: https://github.com/boostorg/regex/issues/247  

This source  
  
```c++  
#include <boost/regex.hpp>  
#include <boost/regex/icu.hpp>  
  
int main()  
{  
	boost::u32regex re(boost::make_u32regex(R"(All.*)"));  
	auto pattern = re.str(); // fails  
  
	return 0;  
}  
```  
when built with `clang++ -I /opt/homebrew/include -I /opt/homebrew/opt/icu4c/include -std=c++17 test.cpp -o test`   
fails with   
```  
In file included from test.cpp:1:  
In file included from /opt/homebrew/include/boost/regex.hpp:34:  
In file included from /opt/homebrew/include/boost/regex/v5/regex.hpp:29:  
In file included from /opt/homebrew/include/boost/regex/v5/regex_workaround.hpp:29:  
In file included from /opt/homebrew/include/boost/throw_exception.hpp:21:  
In file included from /opt/homebrew/include/boost/exception/exception.hpp:9:  
In file included from /opt/homebrew/include/boost/assert/source_location.hpp:13:  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/v1/string:821:42: error: implicit instantiation of undefined template 'std::char_traits<int>'  
  821 |   static_assert(is_same<_CharT, typename traits_type::char_type>::value,  
      |                                          ^  
test.cpp:7:20: note: in instantiation of template class 'std::basic_string<int>' requested here  
    7 |         auto pattern = re.str(); // may fail  
      |                           ^  
```   
on latest AppleClang 17.0.0.17000013.  
  
According to [MS STL issue #4935](https://github.com/microsoft/STL/issues/4935), libc++ 19.0 has restricted instantiations for `std::char_traits<charT>` to the standard character types `char`, `wchar_t`, `char8_t`, `char16_t`, `char32_t`, which lets `boost::u32regex::str()` fail here:      
```c++  
std::basic_string<charT>  str()const  
{  
    return m_pimpl.get() ? m_pimpl->str() : std::basic_string<charT>();  
}  
```  
  
Tested with boost v1.87 and ICU 77 in Xcode 16.3 / AppleClang 17.0.0

---

## Comment 1

> Username: JoachimSchurig  
> Created at: 2025-04-04 15:12:23 UTC  
> Updated at: 2025-04-05 00:18:44 UTC  
> Url: https://github.com/boostorg/regex/issues/247#issuecomment-2779017835  

This is the commit to libc++ that introduced the breaking change: [llvm PR #72694](https://github.com/llvm/llvm-project/pull/72694)

---

## Comment 2

> Username: JoachimSchurig  
> Created at: 2025-04-05 00:09:00 UTC  
> Url: https://github.com/boostorg/regex/issues/247#issuecomment-2779931123  

and this is the followup (probably on this report) in the llvm issue tracker: [llvm #134425](https://github.com/llvm/llvm-project/issues/134425)
