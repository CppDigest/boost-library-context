# #436 - string length = sizeof(S::get()) / sizeof(S::get()[0])  - 1 [Open]

> Username: andry81  
> Created at: 2019-02-07 20:13:20 UTC  
> Updated at: 2019-02-08 12:20:15 UTC  
> Url: https://github.com/boostorg/hana/issues/436  

https://github.com/boostorg/hana/blob/1aa52a97269c1f57ca779e9deed03d8d49a8796d/include/boost/hana/string.hpp#L98  
  
        //////////////////////////////////////////////////////////////////////////  
        // BOOST_HANA_STRING  
        //////////////////////////////////////////////////////////////////////////  
        namespace string_detail {  
            template <typename S, std::size_t ...N>  
            constexpr string<S::get()[N]...>  
            prepare_impl(S, std::index_sequence<N...>)  
            { return {}; }  
  
            template <typename S>  
            constexpr decltype(auto) prepare(S s) {  
                return prepare_impl(s,  
                    std::make_index_sequence<sizeof(S::get()) - 1>{});  
            }  
        }  
  
    #define BOOST_HANA_STRING(s)                                                \  
        (::boost::hana::string_detail::prepare([]{                              \  
            struct tmp {                                                        \  
                static constexpr decltype(auto) get() { return s; }             \  
            };                                                                  \  
            return tmp{};                                                       \  
        }()))                                                                   \  
    /**/  
  
Is't that a risky way to calculate length like that `sizeof(S::get()) - 1`? What about `BOOST_HANA_STRING(L"...")`, is that ok or may be potential a bug in the length? I know the string mostly the `char` oriented, but that macro can be used potentially with any string.

---

## Comment 1

> Username: ricejasonf  
> Created at: 2019-02-07 21:04:30 UTC  
> Url: https://github.com/boostorg/hana/issues/436#issuecomment-461595070  

I see that only `operator""_s` is documented to support only narrow string literals. I wonder if that was omitted from the macro's documentation by mistake.  
  
This closed issue https://github.com/boostorg/hana/issues/80 appears to suggest that only narrow string literals are supported across the board, which makes sense looking at the implementation.  
  
Perhaps we should just add the note to the macro's documentation.
