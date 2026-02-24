# #202 - Boost.Regex/ICU will no longer compile with clang 19 [Closed]

> Username: pkl97  
> Created at: 2024-02-13 17:01:02 UTC  
> Updated at: 2024-07-19 06:49:33 UTC  
> Closed at: 2024-03-25 16:56:57 UTC  
> Url: https://github.com/boostorg/regex/issues/202  

This simple test program triggers a warning in clang 18 that indicates that the code will no longer compile with clang 19:  
```c++  
#include <iostream>  
#include <boost/regex/icu.hpp>  
  
int main()  
{  
    const boost::u32regex r1(boost::make_u32regex(".*"));  
    const boost::u32regex r2(boost::make_u32regex(".*"));  
  
    std::cout << "identical: " << (r1==r2?"yes":"no") << std::endl;  
    return 0;  
}  
```  
  
> In file included from main.cpp:1:  
In file included from include/c++/v1/iostream:43:  
In file included from include/c++/v1/ios:221:  
In file included from include/c++/v1/__fwd/ios.h:13:  
include/c++/v1/__fwd/string.h:45:41: warning: 'char_traits<int>' is deprecated: char_traits<T> for T not equal to char, wchar_t, char8_t, char16_t or char32_t is non-standard and is provided for a temporary period. It will be removed in LLVM 19, so please migrate off of it. [-Wdeprecated-declarations]  
   45 | template <class _CharT, class _Traits = char_traits<_CharT>, class _Allocator = allocator<_CharT> >  
      |                                         ^  
Boost/1.83.0/include/boost/regex/v5/basic_regex.hpp:237:24: note: in instantiation of default argument for 'basic_string<int>' required here  
  237 |          result = std::basic_string<charT>(this->m_expression, this->m_expression_len);  
      |                        ^~~~~~~~~~~~~~~~~~~  
Boost/1.83.0/include/boost/regex/v5/basic_regex.hpp:466:39: note: in instantiation of member function 'boost::re_detail_500::basic_regex_implementation<int, boost::icu_regex_traits>::str' requested here  
  466 |       return m_pimpl.get() ? m_pimpl->str() : std::basic_string<charT>();  
      |                                       ^  
Boost/1.83.0/include/boost/regex/v5/basic_regex.hpp:536:14: note: in instantiation of member function 'boost::basic_regex<int, boost::icu_regex_traits>::str' requested here  
  536 |       return str().compare(that.str());  
      |              ^  
Boost/1.83.0/include/boost/regex/v5/basic_regex.hpp:540:14: note: in instantiation of member function 'boost::basic_regex<int, boost::icu_regex_traits>::compare' requested here  
  540 |       return compare(e) == 0;  
      |              ^  
main.cpp:9:38: note: in instantiation of member function 'boost::basic_regex<int, boost::icu_regex_traits>::operator==' requested here  
  
The problem seems to be the character type UChar32 (which is actually int32_t) when Boost.Regex 1.83.0 is used in combination with the ICU library.

---

## Comment 1

> Username: pkl97  
> Created at: 2024-07-19 06:49:33 UTC  
> Url: https://github.com/boostorg/regex/issues/202#issuecomment-2238481674  

@jzmaddock  
  
I tested with a main branch version of the upcoming clang 19. The comparison problem is indeed fixed and the code works fine. Thanks for that!  
  
However this simple program still fails to compile:  
```c++  
#include <iostream>  
#include <boost/regex/icu.hpp>  
  
int main()  
{  
    boost::make_u32regex(".*").str();  
  
    return 0;  
}  
```  
  
It cannot work because str() in the ICU case returns std::basic_string\<UChar32\> which is no longer supported by the compiler.  
How should Boost.Regex users deal with this issue?  
  
In our case we used str() to convert from UTF-32 to UTF-8 because this is our internal encoding. We implemented a workaround by performing an iteration over the regular expression (with begin()/end()) and converting each UTF-32 character individually to UTF-8. This way we avoid the need for an actual string type.  
  
Wouldn't it be simpler (and maybe also cleaner) to just switch the ICU case to std::basic_string\<char32_t\> instead of using UChar32? Yes, there is a signedness issue, because UChar32 is not unsigned (although the name suggests otherwise). But this could be dealt with inside the library without affecting users. And char32_t is available since C++11, so it is not a new feature that nobody can use.
