# #219 - One member aggregate breaks fields_count [Closed]

> Username: 12AT7  
> Created at: 2025-07-29 22:03:30 UTC  
> Updated at: 2025-12-05 14:37:20 UTC  
> Closed at: 2025-12-05 14:37:20 UTC  
> Url: https://github.com/boostorg/pfr/issues/219  

There appears to be no matching `assert_first_not_base` for aggregates with exactly one member.  This is what exists now in `detail/fields_count.hpp`:  
  
```c++  
template <class T, std::size_t I0, std::size_t... I, class /*Enable*/ = typename std::enable_if<!std::is_copy_constructible<T>::value>::type>  
constexpr auto assert_first_not_base(std::index_sequence<I0, I...>) noexcept  
    -> typename std::add_pointer<decltype(T{ ubiq_rref_base_asserting<T>{}, ubiq_rref_constructor{I}... })>::type  
{  
    return nullptr;  
}  
  
template <class T>  
constexpr void* assert_first_not_base(std::index_sequence<>) noexcept  
{  
    return nullptr;  
}  
```  
and I wonder if we also need:   
```c++  
  
template <class T>  
constexpr auto assert_first_not_base(std::index_sequence<0UL>) noexcept  
    -> typename std::add_pointer<decltype(T{ ubiq_rref_base_asserting<T>{}})>::type  
{  
    return nullptr;  
}  
```  
to cover this case.  In my situation, I hacked this in and it seems to work.

---

## Comment 1

> Username: apolukhin  
> Created at: 2025-10-18 12:23:11 UTC  
> Url: https://github.com/boostorg/pfr/issues/219#issuecomment-3418389491  

@12AT7 could you please provide a test case and a fix as a PR?

---

## Comment 2

> Username: apolukhin  
> Created at: 2025-12-05 14:37:20 UTC  
> Url: https://github.com/boostorg/pfr/issues/219#issuecomment-3617186053  

At he moment the code contains the following lines:  
  
https://github.com/boostorg/pfr/blob/7aa41ed4f5aaa7e52570030e7b28f28cede4f9c1/include/boost/pfr/detail/fields_count.hpp#L148-L160  
  
In the above snippet the `std::index_sequence<0UL>` would match `std::index_sequence<I0, I...>` with `I0 == 0UL and sizeof...(I) == 0`. So the overload seems to be not required.  
  
Feel free to reopen this issue if I'm mistaken. Please provide a test case for the latter case
