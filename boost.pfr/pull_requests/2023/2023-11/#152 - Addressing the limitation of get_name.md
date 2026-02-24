# #152 Addressing the limitation of get_name [Merged]

> Username: schaumb  
> Created at: 2023-11-30 09:11:32 UTC  
> Updated at: 2024-01-28 08:53:17 UTC  
> Merged at: 2024-01-28 08:52:58 UTC  
> Closed at: 2024-01-28 08:52:58 UTC  
> Url: https://github.com/boostorg/pfr/pull/152  

resolves #151

---

## Comment 1

> Username: schaumb  
> Created_at: 2023-11-30 09:38:01 UTC  
> Updated_at: 2023-11-30 22:26:59 UTC  
> Url: https://github.com/boostorg/pfr/pull/152#issuecomment-1833408074  

I found another way to work "fake object" without requiring C++20 (template variable):  
  
```cpp  
template<class T>  
struct X {  
   const T value;  
   static const X<T> report_if_you_see_link_error_with_this_object; // this is not defined, only declared. equivalent with template varaible  
};  
  
template <class T>  
constexpr const T& fake_object() noexcept {  
    return X<T>::report_if_you_see_link_error_with_this_object.value;  
}  
```  
  
This is equivalent to `prf::detail::unsafe_declval` without the [nullptr dereference hack](https://github.com/boostorg/pfr/blob/98789f610a8bab53d5523157ff6a3fac7606764a/include/boost/pfr/detail/unsafe_declval.hpp#L28-L30).  
  
[I created already the changes](https://github.com/schaumb/pfr/pull/1/files). Can I try to apply these modifications too?  
  
What do you think @denzor200 ?

---

## Comment 2

> Username: apolukhin  
> Created_at: 2024-01-28 08:53:17 UTC  
> Url: https://github.com/boostorg/pfr/pull/152#issuecomment-1913520125  

Looks very good!  
  
Many thanks for the PR!

---
