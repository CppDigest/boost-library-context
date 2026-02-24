# #347 - Support instantiating hana strings from const char* [Closed]

> Username: compiletime  
> Created at: 2017-05-19 00:14:37 UTC  
> Updated at: 2018-02-27 12:56:26 UTC  
> Closed at: 2018-02-27 12:56:26 UTC  
> Url: https://github.com/boostorg/hana/issues/347  

Parameter packs of char can be constructed from const char*. As an example:  
```c++  
#include <utility>  
  
template <char... cs>  
struct str {};  
  
constexpr std::size_t length(char const* c) {  
  std::size_t i = 0u;  
  while (*c != 0)  
    ++c, ++i;  
  return i;  
}  
  
template <const char* c, std::size_t... Is>  
constexpr auto expand(std::index_sequence<Is...>) {  
  return str<c[Is]...>{};  
}  
  
template <char const* c>  
struct to_str {  
  using type = decltype(expand<c>(std::make_index_sequence<length(c)>{}));  
};  
  
//-----------------------------------------------------------------------------  
  
#include <type_traits>  
  
using hello_t = str<'h','e','l','l','o',',',' ','w','o','r','l','d'>;  
constexpr const char hello[] = "hello, world";  
static_assert(std::is_same<to_str<hello>::type, hello_t>::value);  
  
int main() {}  
```
