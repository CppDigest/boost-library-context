# #19 - BOOST_METAPARSE_STRING_VALUE can be implemented without length limitation since C++17 [Open]

> Username: denzor200  
> Created at: 2025-07-05 00:26:40 UTC  
> Updated at: 2025-08-31 14:44:32 UTC  
> Url: https://github.com/boostorg/metaparse/issues/19  

We don't need to define `BOOST_METAPARSE_LIMIT_STRING_SIZE` macro anymore. Also I think compilation time will be reduced(haven't measured yet, but I believe).  
```  
#include <boost/metaparse/string.hpp>  
  
template<typename CharT, size_t N> struct fixed_string {  
    const CharT* data;  
    constexpr fixed_string(const CharT (&s)[N]) noexcept:  
      data{s} {  
    }  
    constexpr auto operator[](size_t i) const noexcept { return data[i]; }  
    constexpr size_t size() const noexcept { return N; }  
};  
  
template<typename CharT, size_t N>  
fixed_string(const CharT(&)[N]) -> fixed_string<CharT, N>;  
  
template<const auto& S>  
struct convert_string {  
    template<size_t... Is>  
    static constexpr auto apply(std::index_sequence<Is...>) noexcept {  
            return boost::metaparse::string<S[Is]...>{};  
        }  
};  
  
#define BOOST_METAPARSE_STRING_VALUE(S) ([]{                                          \  
        static constexpr fixed_string fs(S);                                          \  
        return convert_string<fs>::apply(std::make_index_sequence<fs.size() - 1>{});  \  
    }())  
```  
  
The full snippet to play: https://godbolt.org/z/j117Karjq  
  
Only one limitation - `BOOST_METAPARSE_STRING_VALUE` can't be used in `decltype` expressions:  
```  
decltype(BOOST_METAPARSE_STRING_VALUE("test")) a;  // error: lambda-expression in unevaluated context only available with '-std=c++20' or '-std=gnu++20'  
```  
The workaround is to create temporary variable:  
```  
auto t = BOOST_METAPARSE_STRING_VALUE("test");  
decltype(t) a;  // OK  
```  
  
If it's interested, I can create PR.

---

## Comment 1

> Username: sabel83  
> Created at: 2025-07-05 06:56:27 UTC  
> Url: https://github.com/boostorg/metaparse/issues/19#issuecomment-3038320620  

This is a good idea, but the unevaluated context is the original purpose of `BOOST_METAPARSE_STRING` (Boost.Metaparse is for parsing with template metaprograms). ˋBOOST_METAPARSE_STRING_VALUEˋ is a nice to have feature on top of it. So this should only be enabled in standards where it works in an unevaluated context as well. If you take a look at the implementation of ˋBOOST_METAPARSE_STRINGˋ, it already has a framework for standard version specific implementations, so this can be extended to drop the need for the length limit where possible.

---

## Comment 2

> Username: denzor200  
> Created at: 2025-08-31 14:44:32 UTC  
> Url: https://github.com/boostorg/metaparse/issues/19#issuecomment-3240194639  

Created PR: https://github.com/boostorg/metaparse/pull/20
