# #535 - warning: identifier '_c' preceded by whitespace in a literal operator declaration is deprecated [Open]

> Username: RFoe  
> Created at: 2025-10-11 01:13:55 UTC  
> Updated at: 2026-02-16 07:27:47 UTC  
> Url: https://github.com/boostorg/hana/issues/535  

boost-hana 1.88  
clang 20.1.0  
c++26  
  
```cxx  
// boost/bool.h: 198  
    namespace literals {  
        template <char ...c>  
        constexpr auto operator"" _c() {  
            return hana::llong<ic_detail::parse<sizeof...(c)>({c...})>{};  
        }  
    }  
```  
```bash  
/boost/hana/bool.hpp:198:35: warning: identifier '_c' preceded by whitespace in a literal operator declaration is deprecated  
```

---

## Comment 1

> Username: tanmmaaayyyy  
> Created at: 2026-02-16 07:27:47 UTC  
> Url: https://github.com/boostorg/hana/issues/535#issuecomment-3906866192  

Hi,  
  
I looked into this warning. It seems to be caused by the whitespace between `operator""` and `_c` in the literal operator declaration:  
  
```cpp  
constexpr auto operator"" _c()
