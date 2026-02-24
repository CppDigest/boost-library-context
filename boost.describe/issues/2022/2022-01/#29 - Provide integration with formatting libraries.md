# #29 - Provide integration with formatting libraries [Closed]

> Username: Julien-Blanc-tgcm  
> Created at: 2022-01-21 07:51:02 UTC  
> Updated at: 2022-02-13 01:44:33 UTC  
> Closed at: 2022-02-13 01:44:32 UTC  
> Url: https://github.com/boostorg/describe/issues/29  

Since the library provides a to_string / from_string method, it also makes sense that it provides integration with c++20 format library. Maybe at least as a sample in the documentation.  
  
An example using libfmt (i currently don't have local access to a compiler with std::format support):  
```cpp  
template<typename Type>  
struct fmt::formatter<Type,  
        std::enable_if_t<  
        std::is_enum<Type>::value &&  
        std::is_same<decltype(boost::mp11::mp_front<boost::describe::describe_enumerators<Type>>::name), char const* const>::value,  
                char>  
        > : formatter<std::string_view>  
{  
        template<typename FormatContext> auto format(Type v, FormatContext& ctx) -> decltype(ctx.out()) {  
                std::string_view name = "unknown";  
                boost::mp11::mp_for_each<boost::describe::describe_enumerators<Type>>([&](auto EnumValue) {  
                        if(EnumValue.value == v)  
                                name = EnumValue.name;  
                });  
                return formatter<std::string_view>::format(name, ctx);  
        }  
};  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2022-02-09 19:03:12 UTC  
> Url: https://github.com/boostorg/describe/issues/29#issuecomment-1034096250  

I've added a fmtlib example for described structs/classes, which parallels the `std::ostream` one. It didn't occur to me to add `enum` support too. It's kind of trivial using `enum_to_string` but not quite when the value doesn't have a name if we want to support all the string_view format specs. May be worth a second example.

---

## Comment 2

> Username: pdimov  
> Created at: 2022-02-13 01:44:32 UTC  
> Url: https://github.com/boostorg/describe/issues/29#issuecomment-1037658564  

A second fmtlib example, this one for enums, added.
