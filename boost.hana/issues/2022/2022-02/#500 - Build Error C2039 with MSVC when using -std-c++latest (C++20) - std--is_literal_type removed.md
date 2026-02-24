# #500 - Build Error C2039 with MSVC when using /std:c++latest (C++20) - std::is_literal_type removed [Closed]

> Username: dongkeren  
> Created at: 2022-02-14 06:47:30 UTC  
> Updated at: 2022-10-21 02:44:46 UTC  
> Closed at: 2022-10-21 02:44:45 UTC  
> Url: https://github.com/boostorg/hana/issues/500  

Using version 1.7.0, boost/hana/traits.hpp line 73 is causing the error:  
  
> constexpr auto is_literal_type = detail::hana_trait<std::is_literal_type>{};  
  
My workaround is simply bypass it:  
  
> #if __cplusplus > 201704L  
>   constexpr auto is_literal_type = detail::hana_trait<std::is_literal_type>{};  
>#endif  
  
Reference:  
https://stackoverflow.com/questions/40351816/deprecated-stdis-literal-type-in-c17

---

## Comment 1

> Username: ldionne  
> Created at: 2022-10-21 02:44:45 UTC  
> Url: https://github.com/boostorg/hana/issues/500#issuecomment-1286385780  

The problem is that MSVC doesn't set the value of `__cplusplus` correctly unless you pass `/Zc:__cplusplus`: https://godbolt.org/z/c6YKTr8dh
