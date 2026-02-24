# #16 - Generation of variants is probably broken if multiple alternatives have the same type [Closed]

> Username: tzlaine  
> Created at: 2022-03-22 03:01:02 UTC  
> Updated at: 2022-04-15 06:05:40 UTC  
> Closed at: 2022-04-15 06:05:40 UTC  
> Url: https://github.com/boostorg/parser/issues/16  

Unverified.  If this is the case, this is a fix:  
  
```  
    template<typename... Ts>  
    auto to_variant(hana::set<Ts...> const &)  
    {  
        return std::variant<typename Ts::type...>{};  
    }  
  
            auto const types = hana::transform(tuple_of_possible_dupes, [](auto const & x) {  
                return hana::type_c<std::remove_cvref_t<decltype(x)>>;  
            });  
            auto const unique_types = hana::to_set(types);  
            return detail::to_variant(unique_types);  
```

---

## Comment 1

> Username: tzlaine  
> Created at: 2022-04-15 06:05:40 UTC  
> Url: https://github.com/boostorg/parser/issues/16#issuecomment-1099880086  

Confirmed that this is not an issue.
