# #69 - Feature request: for_each_field multiple visitation [Open]

> Username: invexed  
> Created at: 2021-02-12 11:55:59 UTC  
> Updated at: 2021-06-14 22:13:35 UTC  
> Url: https://github.com/boostorg/pfr/issues/69  

Multiple visitation is easy enough to achieve with a library like Boost.Hana (see below), but I think it would be useful functionality for ```for_each_field``` to support directly.  
  
```cpp  
template<typename Invocable, typename... Aggregates>  
constexpr auto for_each_field(Invocable f, Aggregates&&... xs) -> void  
{  
    namespace hana = boost::hana;  
    hana::for_each(hana::zip(boost::pfr::structure_tie(xs)...), hana::fuse(std::move(f)));  
}  
```  
[Example on Compiler Explorer](https://godbolt.org/z/K9Krfh)

---

## Comment 1

> Username: invexed  
> Created at: 2021-03-12 15:36:59 UTC  
> Url: https://github.com/boostorg/pfr/issues/69#issuecomment-797567755  

```boost::hana::zip_with``` and ```std::forward_as_tuple``` would prevent unnecessary copies occurring when constructing the inner tuples.

---

## Comment 2

> Username: SpriteOvO  
> Created at: 2021-06-14 22:06:04 UTC  
> Updated at: 2021-06-14 22:13:35 UTC  
> Url: https://github.com/boostorg/pfr/issues/69#issuecomment-861026157  

`pfr::structure_tie` with parameter pack will cause an "internal compiler error" in the current latest MSVC, for those who are having this problem or don't want to use `hana`, the following workaround also works:  
  
```cpp  
template <size_t index = 0, class ...Args>  
void ForEachMultipleFields(const auto &callback, Args &&...args)  
{  
    callback(pfr::get<index>(args)...);  
  
    constexpr size_t size = pfr::detail::fields_count<  
        std::decay_t<std::tuple_element_t<0, std::tuple<Args...>>>  
    >();  
    if constexpr (index + 1 != size) {  
        ForEachMultipleFields<index + 1>(callback, std::forward<Args>(args)...);  
    }  
}  
```  
  
[Example](https://godbolt.org/z/16dTnW5Kr)
