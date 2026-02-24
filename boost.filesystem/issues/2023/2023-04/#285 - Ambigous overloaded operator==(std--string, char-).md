# #285 - Ambigous overloaded operator==(std::string, char*) [Closed]

> Username: sgn  
> Created at: 2023-04-26 01:50:36 UTC  
> Updated at: 2023-05-07 23:14:04 UTC  
> Closed at: 2023-05-07 23:14:04 UTC  
> Url: https://github.com/boostorg/filesystem/issues/285  

Not sure if I should this issue should be reported here or reported to the other upstream, but this short example is failed to be compiled:  
```cpp  
#include <string>  
#include <boost/filesystem.hpp>  
  
using namespace boost::filesystem;  
  
bool f(char *arg) {  
	return std::string("a-str") == arg;  
}  
```  
  
GNU's `g++` reports:  
> test.cpp: In function 'bool f(char*)':  
> test.cpp:7:40: warning: ISO C++ says that these are ambiguous, even though the worst conversion for the first is better than the worst conversion for the second:  
>     7 |         return std::string("a-str") == arg;  
>       |                                        ^~~  
> In file included from /usr/include/c++/12.2/string:53,  
>                  from test.cpp:1:  
> /usr/include/c++/12.2/bits/basic_string.h:3599:5: note: candidate 1: 'bool std::operator==(const __cxx11::basic_string<_CharT, _Traits, _Allocator>&, const _CharT*) [with _CharT = char; _Traits = char_traits<char>; _Alloc = allocator<char>]'  
>  3599 |     operator==(const basic_string<_CharT, _Traits, _Alloc>& __lhs,  
>       |     ^~~~~~~~  
> In file included from /usr/include/boost/filesystem.hpp:16,  
>                  from test.cpp:2:  
> /usr/include/boost/filesystem/path.hpp:1294:9: note: candidate 2: 'typename boost::enable_if_c<boost::filesystem::detail::path_traits::is_convertible_to_path_source<typename boost::remove_cv<T>::type>::value, bool>::type boost::filesystem::operator==(const path&, const Source&) [with Source = char*; typename boost::enable_if_c<detail::path_traits::is_convertible_to_path_source<typename boost::remove_cv<T>::type>::value, bool>::type = bool; typename boost::remove_cv<T>::type = char*]'  
>  1294 | >::type operator==(path const& lhs, Source const& rhs)  
>       |         ^~~~~~~~  
  
`clang++` reports:  
> test.cpp:7:30: error: use of overloaded operator '==' is ambiguous (with operand types 'std::string' (aka 'basic_string<char>') and 'char *')  
>         return std::string("a-str") == arg;  
>                ~~~~~~~~~~~~~~~~~~~~ ^  ~~~  
> /usr/bin/../lib64/gcc/x86_64-linux-gnu/12.2/../../../../include/c++/12.2/bits/basic_string.h:3599:5: note: candidate function [with _CharT = char, _Traits = std::char_traits<char>, _Alloc = std::allocator<char>]  
>     operator==(const basic_string<_CharT, _Traits, _Alloc>& __lhs,  
>     ^  
> /usr/include/boost/filesystem/path.hpp:1294:9: note: candidate function [with Source = char *]  
> >::type operator==(path const& lhs, Source const& rhs)  
>         ^  
> /usr/include/boost/filesystem/path.hpp:1303:9: note: candidate function [with Source = std::basic_string<char>]  
> >::type operator==(Source const& lhs, path const& rhs)  
>         ^  
> 1 error generated.

---

## Comment 1

> Username: Lastique  
> Created at: 2023-04-26 07:50:07 UTC  
> Url: https://github.com/boostorg/filesystem/issues/285#issuecomment-1522945426  

I think this should work if you remove `using namespace boost::filesystem;`.
