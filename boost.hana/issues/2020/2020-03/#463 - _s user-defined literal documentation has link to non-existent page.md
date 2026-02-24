# #463 - _s user-defined literal documentation has link to non-existent page. [Closed]

> Username: jonesmz  
> Created at: 2020-03-15 08:20:03 UTC  
> Updated at: 2020-08-26 18:22:05 UTC  
> Closed at: 2020-08-26 18:22:05 UTC  
> Url: https://github.com/boostorg/hana/issues/463  

```cpp  
#ifdef BOOST_HANA_CONFIG_ENABLE_STRING_UDL  
    //////////////////////////////////////////////////////////////////////////  
    // _s user-defined literal  
    //////////////////////////////////////////////////////////////////////////  
    namespace literals {  
        template <typename CharT, CharT ...s>  
        constexpr auto operator"" _s() {  
            static_assert(std::is_same<CharT, char>::value,  
            "hana::string: Only narrow string literals are supported with "  
            "the _s string literal right now. See https://goo.gl/fBbKD7 "  
            "if you need support for fancier types of compile-time strings.");  
            return hana::string_c<s...>;  
        }  
    }  
#endif  
```

---

## Comment 1

> Username: invexed  
> Created at: 2020-04-12 10:36:05 UTC  
> Url: https://github.com/boostorg/hana/issues/463#issuecomment-612594781  

The link should resolve to [https://github.com/boostorg/hana/issues/80](url).
